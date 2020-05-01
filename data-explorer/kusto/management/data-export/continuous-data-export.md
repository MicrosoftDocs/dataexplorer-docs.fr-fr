---
title: Exportation de données en continu-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’exportation continue de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/27/2020
ms.openlocfilehash: 7abcead19e0306853bc6a585a41b5b79657a6842
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617712"
---
# <a name="continuous-data-export"></a>Exportation de données continue

Exportez en continu des données à partir de Kusto vers une [table externe](../externaltables.md). La table externe définit la destination (par exemple, le stockage d’objets BLOB Azure) et le schéma des données exportées. Les données exportées sont définies par une requête exécutée périodiquement. Les résultats sont stockés dans la table externe. Le processus garantit que tous les enregistrements sont exportés « exactement une fois » (à l’exception des tables de dimension, dans lesquelles tous les enregistrements sont évalués dans toutes les exécutions). 

L’exportation de données continue vous oblige à [créer une table externe](../externaltables.md#create-or-alter-external-table) , puis à [créer une définition d’exportation continue](#create-or-alter-continuous-export) pointant vers la table externe. 

> [!NOTE] 
> * Kusto ne prend pas en charge l’exportation des enregistrements historiques ingérés avant la création de l’exportation continue (dans le cadre de l’exportation continue). Les enregistrements historiques peuvent être exportés séparément à l’aide de la [commande d’exportation](export-data-to-an-external-table.md)(non continue). Pour plus d’informations, consultez [exportation de données historiques](#exporting-historical-data). 
> * L’exportation continue ne fonctionne pas pour les données ingérées à l’aide de l’ingestion de diffusion en continu. 
> * Actuellement, l’exportation continue ne peut pas être configurée sur une table sur laquelle une [stratégie de sécurité au niveau des lignes](../../management/rowlevelsecuritypolicy.md) est activée.
> * L’exportation continue n’est pas prise en charge `impersonate` pour les tables externes avec dans leurs [chaînes de connexion](../../api/connection-strings/storage.md).
 
## <a name="notes"></a>Notes

* La garantie d’exportation « exactement une fois » concerne *uniquement* les fichiers signalés dans la [commande Afficher les artefacts exportés](#show-continuous-export-exported-artifacts). 
L’exportation continue ne garantit *pas* que chaque enregistrement sera écrit une seule fois dans la table externe. Si une défaillance se produit après le début de l’exportation et que certains des artefacts ont déjà été écrits dans la table externe, la table externe _peut_ contenir des doublons (ou même des fichiers endommagés, au cas où une opération d’écriture a été abandonnée avant la fin). Dans ce cas, les artefacts ne sont pas supprimés de la table externe, mais ils ne sont *pas* signalés dans la [commande Afficher les artefacts exportés](#show-continuous-export-exported-artifacts). La consommation des fichiers exportés `show exported artifacts command` à l’aide de ne garantit pas de doublons (et non d’altérations, bien sûr).
* Pour garantir une exportation « exactement une fois », l’exportation continue utilise des [curseurs de base de données](../databasecursor.md). 
La [stratégie IngestionTime](../ingestiontime-policy.md) doit donc être activée sur toutes les tables référencées dans la requête qui doivent être traitées « exactement une fois » dans l’exportation. La stratégie est activée par défaut sur toutes les tables nouvellement créées.
* Le schéma de sortie de la requête d’exportation *doit* correspondre au schéma de la table externe vers laquelle vous exportez. 
* L’exportation continue ne prend pas en charge les appels entre bases de données et clusters.
* L’exportation continue s’exécute en fonction de la période configurée pour celle-ci. La valeur recommandée pour cet intervalle est d’au moins plusieurs minutes, selon les latences que vous êtes prêt à accepter. L’exportation continue *n’est pas* conçue pour diffuser en continu des données à partir de Kusto. Il s’exécute en mode distribué, où tous les nœuds sont exportés simultanément. Si la plage de données interrogée par chaque exécution est faible, la sortie de l’exportation continue serait un grand nombre de petits artefacts (le nombre dépend du nombre de nœuds dans le cluster). 
* Le nombre d’opérations d’exportation qui peuvent s’exécuter simultanément est limité par la capacité d’exportation des données du cluster (voir [limitation](../../management/capacitypolicy.md#throttling)). Si le cluster ne dispose pas d’une capacité suffisante pour gérer toutes les exportations continues, d’autres démarreront en retard. 
 
* Par défaut, toutes les tables référencées dans la requête d’exportation sont supposées être des [tables de faits](../../concepts/fact-and-dimension-tables.md). 
Par conséquent, elles sont *étendues* au curseur de base de données. Les enregistrements inclus dans la requête d’exportation sont uniquement ceux qui sont joints depuis l’exécution de l’exportation précédente. 
La requête d’exportation peut contenir des [tables de dimension](../../concepts/fact-and-dimension-tables.md) dans lesquelles *tous les* enregistrements de la table de dimension sont inclus dans *toutes les* requêtes d’exportation. 
    * Lors de l’utilisation de jointures entre les tables de faits et de dimension en mode d’exportation continu, vous devez garder à l’esprit que les enregistrements de la table de faits ne sont traités qu’une seule fois : si l’exportation s’exécute alors que des enregistrements dans les tables de dimension sont absents pour certaines clés, les enregistrements des clés respectives seront ignorés ou incluront des valeurs NULL pour les colonnes de dimension des fichiers exportés (selon que la requête utilise une jointure interne La propriété forcedLatency dans la définition d’exportation continue peut être utile dans de tels cas, où les tables de faits et de dimensions sont ingérées en même temps (pour les enregistrements correspondants).
    * L’exportation continue d’une seule table de dimension n’est pas prise en charge. La requête d’exportation doit inclure au moins une table de faits unique.
    * La syntaxe déclare explicitement quelles tables sont étendues (fait) et qui ne sont pas étendues (dimension). Pour plus `over` d’informations, consultez le paramètre dans la [commande CREATE](#create-or-alter-continuous-export) .

* Le nombre de fichiers exportés dans chaque itération d’exportation continue dépend de la façon dont la table externe est partitionnée. Pour plus d’informations, reportez-vous à la section Remarques de la [commande exporter vers une table externe](export-data-to-an-external-table.md) . Notez que chaque itération d’exportation continue écrit toujours dans de nouveaux fichiers et n’est jamais ajoutée à *des* fichiers existants. Par conséquent, le nombre de fichiers exportés dépend également de la fréquence d’exécution de l’exportation continue`intervalBetweenRuns` (paramètre).

Toutes les commandes d’exportation continue requièrent des [autorisations d’administrateur de base de données](../access-control/role-based-authorization.md).

## <a name="create-or-alter-continuous-export"></a>Créer ou modifier une exportation continue

**Syntaxe :**

`.create-or-alter``continuous-export` *ContinuousExportName* <br>
[ `over` `(` *T1*, *T2* `)`] <br>
`to``table` *ExternalTableName* <br> [ `with` `(` *PropertyName* PropertyName `=` *PropertyValue*PropertyValue`,`... `)`]<br>
\<| *Demande*

**Propriétés** :

| Propriété             | Type     | Description   |
|----------------------|----------|---------------------------------------|
| ContinuousExportName | String   | Nom de l’exportation continue. Le nom doit être unique dans la base de données et utilisé pour exécuter régulièrement l’exportation continue.      |
| ExternalTableName    | String   | Nom de la [table externe](../externaltables.md) vers laquelle effectuer l’exportation.  |
| Requête                | String   | Requête à exporter.  |
| sur (T1, T2)        | String   | Liste facultative de tables de faits séparées par des virgules dans la requête. Si cette valeur n’est pas spécifiée, toutes les tables référencées dans la requête sont supposées être des tables de faits. S’ils sont spécifiés, les tables qui *ne sont pas* dans cette liste sont traitées comme des tables de dimension et ne sont pas étendues (tous les enregistrements participent à toutes les exportations). Pour plus d’informations, consultez la [section Remarques](#notes) . |
| intervalBetweenRuns  | Timespan | Intervalle de temps entre les exécutions d’exportation continue. Doit être supérieur à 1 minute.   |
| forcedLatency        | Timespan | Durée facultative pour limiter la requête aux enregistrements qui ont été ingérés uniquement avant cette période (par rapport à l’heure actuelle). Cela est utile si, par exemple, la requête effectue des agrégations/jointures et que vous souhaitez vous assurer que tous les enregistrements appropriés ont déjà été ingérés avant d’exécuter l’exportation.

En plus de ce qui précède, toutes les propriétés prises en charge dans la [commande exporter vers une table externe](export-data-to-an-external-table.md) sont prises en charge dans la commande d’exportation continue. 

**Exemple :**

```kusto
.create-or-alter continuous-export MyExport
over (T)
to table ExternalBlob
with
(intervalBetweenRuns=1h, 
 forcedLatency=10m, 
 sizeLimit=104857600)
<| T
```

| Nom     | ExternalTableName | Requête | ForcedLatency | IntervalBetweenRuns | CursorScopedTables         | ExportProperties                   |
|----------|-------------------|-------|---------------|---------------------|----------------------------|------------------------------------|
| MyExport | ExternalBlob      | S     | 00:10:00      | 01:00:00            | [<br>  "['DB']. ['] "<br>] | {<br>  « SizeLimit » : 104857600<br>} |

## <a name="show-continuous-export"></a>Afficher l’exportation continue

**Syntaxe :**

`.show``continuous-export` *ContinuousExportName*

Retourne les propriétés d’exportation continue de *ContinuousExportName*. 

**Sous**

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue. |


`.show` `continuous-exports`

Retourne toutes les exportations continues de la base de données. 

**Output:**

| Paramètre de sortie    | Type     | Description                                                             |
|---------------------|----------|-------------------------------------------------------------------------|
| CursorScopedTables  | String   | Liste des tables (fait) explicitement délimitées (sérialisées JSON)               |
| ExportProperties    | String   | Propriétés d’exportation (sérialisées JSON)                                     |
| ExportedTo          | DateTime | Dernière date et heure d’ingestion qui a été exportée avec succès       |
| ExternalTableName   | String   | Nom de la table externe                                              |
| ForcedLatency       | TimeSpan | Latence forcée (null si non fourni)                                   |
| IntervalBetweenRuns | TimeSpan | Intervalle entre les exécutions                                                   |
| IsDisabled          | Boolean  | True si l’exportation continue est désactivée                               |
| IsRunning           | Boolean  | True si l’exportation continue est en cours d’exécution                      |
| LastRunResult       | String   | Résultats de la dernière exécution d’exportation continue (`Completed` ou) `Failed` |
| LastRunTime         | DateTime | Heure de la dernière exécution de l’exportation continue (heure de début)           |
| Nom                | String   | Nom de l’exportation continue                                           |
| Requête               | String   | Exporter une requête                                                            |
| StartCursor         | String   | Point de départ de la première exécution de cette exportation continue         |

## <a name="show-continuous-export-exported-artifacts"></a>Afficher les artefacts exportés d’exportation continue

**Syntaxe :**

`.show``continuous-export` *ContinuousExportName*`exported-artifacts`

Retourne tous les artefacts exportés par l’exportation continue dans toutes les exécutions. Il est recommandé de filtrer les résultats selon la colonne timestamp dans la commande pour afficher uniquement les enregistrements intéressants. L’historique des artefacts exportés est conservé pendant 14 jours. 

**Sous**

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue. |

**Output:**

| Paramètre de sortie  | Type     | Description                            |
|-------------------|----------|----------------------------------------|
| Timestamp         | Datetime | Horodateur de l’exécution de l’exportation continue |
| ExternalTableName | String   | Nom de la table externe             |
| Path              | String   | Chemin de sortie                            |
| NumRecords        | long     | Nombre d’enregistrements exportés dans le chemin     |

**Exemple :** 

```kusto
.show continuous-export MyExport exported-artifacts | where Timestamp > ago(1h)
```

| Timestamp                   | ExternalTableName | Path             | NumRecords | SizeInBytes |
|-----------------------------|-------------------|------------------|------------|-------------|
| 2018-12-20 07:31:30.2634216 | ExternalBlob      | `http://storageaccount.blob.core.windows.net/container1/1_6ca073fd4c8740ec9a2f574eaa98f579.csv` | 10                          | 1 024              |

## <a name="show-continuous-export-failures"></a>Afficher les échecs d’exportation continue

**Syntaxe :**

`.show``continuous-export` *ContinuousExportName*`failures`

Retourne tous les échecs enregistrés dans le cadre de l’exportation continue. Filtrez les résultats selon la colonne timestamp dans la commande pour afficher uniquement les plages horaires qui vous intéressent. 

**Sous**

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue  |

**Output:**

| Paramètre de sortie | Type      | Description                                         |
|------------------|-----------|-----------------------------------------------------|
| Timestamp        | Datetime  | Horodateur de l’échec.                           |
| OperationId      | String    | ID d’opération de l’échec.                    |
| Nom             | String    | Nom de l’exportation continue.                             |
| LastSuccessRun   | Timestamp | Dernière exécution réussie de l’exportation continue.   |
| FailureKind      | String    | Échec/PartialFailure. PartialFailure indique que certains artefacts ont été exportés avec succès avant l’échec. |
| Détails          | String    | Détails de l’erreur d’échec.                              |

**Exemple :** 

```kusto
.show continuous-export MyExport failures 
```

| Timestamp                   | OperationId                          | Nom     | LastSuccessRun              | FailureKind | Détails    |
|-----------------------------|--------------------------------------|----------|-----------------------------|-------------|------------|
| 2019-01-01 11:07:41.1887304 | ec641435-2505-4532-ba19-d6ab88c96a9d | MyExport | 2019-01-01 11:06:35.6308140 | Échec     | Détails... |

## <a name="drop-continuous-export"></a>Supprimer l’exportation continue

**Syntaxe :**

`.drop``continuous-export` *ContinuousExportName*

**Sous**

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue |

**Output:**

Les exportations continues restantes dans la base de données (après suppression). Schéma de sortie comme dans la [commande afficher l’exportation continue](#show-continuous-export).

## <a name="disable-or-enable-continuous-export"></a>Désactiver ou activer l’exportation continue

**Syntaxe :**

`.enable``continuous-export` *ContinuousExportName* 

`.disable``continuous-export` *ContinuousExportName* 

Vous pouvez désactiver ou activer le travail d’exportation continue. Une exportation continue désactivée ne sera pas exécutée, mais son état actuel est persistant et peut être repris lorsque l’exportation continue est activée. Lorsque vous activez une exportation continue qui a été désactivée depuis longtemps, l’exportation se poursuit là où elle s’est arrêtée, quand elle est désactivée. Cela peut entraîner une exportation à long terme, bloquant l’exécution d’autres exportations, si la capacité de cluster est insuffisante pour traiter tous les processus. Les exportations continues sont exécutées par heure de la dernière exécution dans l’ordre croissant (l’exportation la plus ancienne s’exécutera en premier, jusqu’à ce que l’opération de rattrapage soit terminée). 

**Sous**

| Propriété             | Type   | Description                |
|----------------------|--------|----------------------------|
| ContinuousExportName | String | Nom de l’exportation continue |

**Output:**

Résultat de la [commande afficher l’exportation continue](#show-continuous-export) de l’exportation continue modifiée. 




## <a name="exporting-historical-data"></a>Exportation de données historiques

L’exportation continue commence à exporter des données uniquement à partir du point de sa création. Les enregistrements ingérés avant cette heure doivent être exportés séparément à l’aide de la [commande d’exportation](export-data-to-an-external-table.md)(non continue). Pour éviter les doublons avec les données exportées par l’exportation continue, utilisez le StartCursor retourné par la [commande afficher l’exportation continue](#show-continuous-export) et exporter uniquement les enregistrements où cursor_before_or_at la valeur de curseur. Reportez-vous à l’exemple ci-dessous. Les données d’historique peuvent être trop volumineuses pour être exportées dans une seule commande d’exportation. Par conséquent, partitionnez la requête en plusieurs lots plus petits. 

```kusto
.show continuous-export MyExport | project StartCursor
```

| StartCursor        |
|--------------------|
| 636751928823156645 |

Suivi de : 

```kusto
.export async to table ExternalBlob
<| T | where cursor_before_or_at("636751928823156645")
```
