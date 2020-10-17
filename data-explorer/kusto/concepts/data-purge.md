---
title: Purge des données-Azure Explorateur de données
description: Cet article décrit la purge des données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 05/12/2020
ms.openlocfilehash: 053581b5109d0eeacd7b69fd0eda2b53f43ac701
ms.sourcegitcommit: 468b4ad125657c5131e4c3c839f702ebb6e455a0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92134742"
---
# <a name="data-purge"></a>Vidage des données

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

En tant que plateforme de données, Azure Explorateur de données prend en charge la possibilité de supprimer des enregistrements individuels, par le biais de l’utilisation de Kusto `.purge` et de commandes associées. Vous pouvez également [purger une table entière](#purging-an-entire-table).  

> [!WARNING]
> La suppression des données via la `.purge` commande est conçue pour être utilisée pour protéger des données personnelles et ne doit pas être utilisée dans d’autres scénarios. Elle n’est pas conçue pour prendre en charge des requêtes de suppression fréquentes, ni pour la suppression de quantités importantes de données, et peut avoir un impact significatif sur les performances du service.

## <a name="purge-guidelines"></a>Instructions de vidage

Concevez soigneusement votre schéma de données et examinez les stratégies pertinentes avant de stocker les données personnelles dans Azure Explorateur de données.

1. Dans le meilleur des cas, la période de rétention sur ces données est suffisamment petite et les données sont automatiquement supprimées.
1. Si l’utilisation de la période de rétention n’est pas possible, isolez toutes les données soumises aux règles de confidentialité dans un petit nombre de tables Azure Explorateur de données. De manière optimale, utilisez une seule table et liez-la à partir de toutes les autres tables. Cette isolation vous permet d’exécuter le [processus de purge](#purge-process) des données sur un petit nombre de tables contenant des données sensibles, et d’éviter toutes les autres tables.
1. L’appelant doit faire chaque tentative de traitement par lot de l’exécution de `.purge` commandes sur 1-2 commandes par table et par jour. N’émettez pas plusieurs commandes avec des prédicats d’identité d’utilisateur uniques. Au lieu de cela, envoyez une commande unique dont le prédicat comprend toutes les identités des utilisateurs qui nécessitent une purge.

## <a name="purge-process"></a>Processus de vidage

Le processus de purge sélective des données à partir d’Azure Explorateur de données se déroule comme suit :

1. Phase 1 : fournir une entrée avec un nom de table Explorateur de données Azure et un prédicat par enregistrement, indiquant les enregistrements à supprimer. Kusto analyse la table pour identifier les étendues de données qui participent à la purge des données. Les étendues identifiées sont celles qui ont un ou plusieurs enregistrements pour lesquels le prédicat retourne la valeur true.
1. Phase 2 : (suppression réversible) remplacez chaque étendue de données de la table (identifiée à l’étape (1)) par une version régérée. La version régérée ne doit pas avoir les enregistrements pour lesquels le prédicat retourne la valeur true. Si de nouvelles données ne sont pas ingérées dans la table, à la fin de cette phase, les requêtes ne retournent plus de données pour lesquelles le prédicat retourne la valeur true. La durée de la phase de suppression réversible de vidage dépend des paramètres suivants : 
     * Nombre d’enregistrements qui doivent être purgés 
     * Enregistrer la distribution dans les étendues de données du cluster 
     * Nombre de nœuds dans le cluster  
     * Capacité de rechange pour les opérations de vidage
     * Plusieurs autres facteurs la durée de la phase 2 peuvent varier de quelques secondes à plusieurs heures.
1. Phase 3 : (suppression définitive) restaurez tous les artefacts de stockage qui peuvent avoir des données « incohérentes », puis supprimez-les du stockage. Cette phase est effectuée au moins cinq jours après la fin de la phase précédente, mais pas plus de 30 jours après la commande initiale. Ces chronologies sont définies pour respecter les exigences de confidentialité des données.

L’émission d’une `.purge` commande déclenche ce processus, qui prend quelques jours. Si la densité des enregistrements auxquels s’applique le prédicat est suffisamment grande, le processus va effectivement obtenir toutes les données de la table. Cette réacquisition a un impact significatif sur les performances et les COGS.

## <a name="purge-limitations-and-considerations"></a>Limitations et considérations relatives à la purge

* Le processus de vidage est définitif et irréversible. Il n’est pas possible d’annuler ce processus ou de récupérer des données purgées. Les commandes telles que [annuler la suppression de table](../management/undo-drop-table-command.md) ne peuvent pas récupérer les données purgées. La restauration des données vers une version antérieure ne peut pas aller à avant la dernière commande de vidage.

* Avant d’exécuter la purge, vérifiez le prédicat en exécutant une requête et en vérifiant que les résultats correspondent au résultat attendu. Vous pouvez également utiliser le processus en deux étapes qui retourne le nombre attendu d’enregistrements qui seront purgés. 

* La `.purge` commande est exécutée sur le point de terminaison gestion des données : `https://ingest-[YourClusterName].[region].kusto.windows.net` .
   La commande nécessite des autorisations d’[administrateur de base de données](../management/access-control/role-based-authorization.md) pour les bases de données appropriées. 
* En raison de l’impact sur les performances du processus de purge, et pour garantir que les  [instructions de purge](#purge-guidelines) ont été suivies, l’appelant doit modifier le schéma de données afin que les tables minimales incluent les données pertinentes et les commandes batch par table pour réduire l’impact CMV significatif du processus de purge.
* Le `predicate` paramètre de la commande [. purge](#purge-table-tablename-records-command) est utilisé pour spécifier les enregistrements à purger.
La taille de `Predicate` est limitée à 63 Ko. Lors de la construction de `predicate` :
    * Utilisez l' [opérateur « in »](../query/inoperator.md), par exemple, `where [ColumnName] in ('Id1', 'Id2', .. , 'Id1000')` . 
    * Notez les limites de l' [opérateur « in »](../query/inoperator.md) (la liste peut contenir jusqu’à `1,000,000` valeurs).
    * Si la taille de la requête est importante, utilisez l' [ `externaldata` opérateur](../query/externaldata-operator.md), par exemple `where UserId in (externaldata(UserId:string) ["https://...blob.core.windows.net/path/to/file?..."])` . Le fichier stocke la liste des ID à purger.
    * La taille totale de la requête, après le développement de tous les `externaldata` objets BLOB (taille totale de tous les objets BLOB), ne peut pas dépasser 64 Mo. 

## <a name="purge-performance"></a>Purger les performances

Une seule demande de vidage peut être exécutée sur le cluster à un moment donné. Toutes les autres demandes sont mises en file d’attente dans l' `Scheduled` État. Surveillez la taille de la file d’attente des demandes de vidage et conservez les limites appropriées pour répondre aux exigences de vos données.

Pour réduire la durée d’exécution de vidage :
* Suivez les [instructions de purge](#purge-guidelines) pour réduire la quantité de données purgées.
* Ajustez la [stratégie de mise en cache](../management/cachepolicy.md) , car le vidage prend plus de temps sur les données à froid.
* Monter en charge le cluster

* Augmentez la capacité de purge de cluster, après mûre réflexion, comme détaillé dans les [Extensions purger la capacité de reconstruction](../management/capacitypolicy.md#extents-purge-rebuild-capacity). La modification de ce paramètre nécessite l’ouverture d’un [ticket de support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="trigger-the-purge-process"></a>Déclencher le processus de vidage

> [!NOTE]
> L’exécution de vidage est appelée en exécutant la commande [purger les enregistrements *TableName* de table](#purge-table-tablename-records-command) sur le point de terminaison gestion des données https://ingest- [YourClusterName]. [ Region]. Kusto. Windows. net.

### <a name="purge-table-tablename-records-command"></a>Commande purger les enregistrements TableName de table

La commande de vidage peut être appelée de deux manières pour différents scénarios d’utilisation :

* Appel programmatique : étape unique destinée à être appelée par des applications. L’appel de cette commande déclenche directement la séquence d’exécution de vidage.

  **Syntaxe**

  ```kusto
  // Connect to the Data Management service
  #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
 
  .purge table [TableName] records in database [DatabaseName] with (noregrets='true') <| [Predicate]
   ```

* Appel humain : processus en deux étapes qui nécessite une confirmation explicite, effectuée dans le cadre d’une étape distincte. Le premier appel de la commande retourne un jeton de vérification, qui doit être fourni pour exécuter le vidage réel. Cette séquence réduit le risque de supprimer par inadvertance des données incorrectes.

 > [!NOTE]
 > La première étape de l’appel en deux étapes requiert l’exécution d’une requête sur l’ensemble du jeu de données, afin d’identifier les enregistrements à purger.
 > Cette requête peut expirer ou échouer sur des tables volumineuses, en particulier avec une quantité importante de données de cache à froid. En cas de défaillance, validez le prédicat vous-même et, après avoir vérifié l’exactitude, utilisez la purge à une étape avec l' `noregrets` option.

  **Syntaxe**

  ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (no records will be purged until step #2 is executed)
     .purge table [TableName] records in database [DatabaseName] <| [Predicate]

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] records in database [DatabaseName] with (verificationtoken='<verification token from step #1>') <| [Predicate]
  ```
    
    | Paramètres  | Description  |
    |---------|---------|
    | `DatabaseName`   |   Nom de la base de données      |
    | `TableName`     |     Nom de la table    |
    | `Predicate`    |    Identifie les enregistrements à purger. Consultez Limitations des prédicats de vidage ci-dessous. | 
    | `noregrets`    |     Si cette valeur est définie, déclenche une activation en une seule étape.    |
    | `verificationtoken`     |  Dans le scénario d’activation en deux étapes (non `noregrets` défini), ce jeton peut être utilisé pour exécuter la deuxième étape et valider l’action. Si `verificationtoken` n’est pas spécifié, il déclenche la première étape de la commande. Les informations relatives à la purge sont retournées avec un jeton qui doit être repassé à la commande pour exécuter l’étape #2.   |

    **Supprimer les limitations de prédicat**

    * Le prédicat doit être une sélection simple (par exemple, *où [ColumnName] = = 'X'*  /  *Where [ColumnName] in ('X', 'Y', 'Z') et [OtherColumn] = = 'a'*).
    * Plusieurs filtres doivent être combinés avec un’and', plutôt que des `where` clauses distinctes (par exemple, `where [ColumnName] == 'X' and  OtherColumn] == 'Y'` et non `where [ColumnName] == 'X' | where [OtherColumn] == 'Y'` ).
    * Le prédicat ne peut pas référencer des tables autres que la table en cours de purge (*TableName*). Le prédicat ne peut inclure que l’instruction de sélection ( `where` ). Il ne peut pas projeter des colonnes spécifiques de la table (schéma de sortie lors de l’exécution de'* `table` | Le prédicat*'doit correspondre au schéma de table.
    * Les fonctions système (telles que, `ingestion_time()` , `extent_id()` ) ne sont pas prises en charge.

#### <a name="example-two-step-purge"></a>Exemple : vidage en deux étapes

Pour démarrer la purge dans un scénario d’activation en deux étapes, exécutez l’étape #1 de la commande :

 ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
     
    .purge table MyTable records in database MyDatabase <| where CustomerId in ('X', 'Y')
 ```

**Sortie**

 | NumRecordsToPurge | EstimatedPurgeExecutionTime| VerificationToken
 |---|---|---
 | 1 596 | 00:00:02 | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b

Ensuite, validez le NumRecordsToPurge avant d’exécuter l’étape #2. 

Pour effectuer une purge dans un scénario d’activation en deux étapes, utilisez le jeton de vérification retourné à partir de l’étape #1 pour exécuter l’étape #2 :

```kusto
.purge table MyTable records in database MyDatabase
 with(verificationtoken='e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b')
<| where CustomerId in ('X', 'Y')
```

**Sortie**

| `OperationId` | `DatabaseName` | `TableName`|`ScheduledTime` | `Duration` | `LastUpdatedOn` |`EngineOperationId` | `State` | `StateDetails` |`EngineStartTime` | `EngineDuration` | `Retries` |`ClientRequestId` | `Principal`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mabdd |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Planifié | | | |0 |KE. RunCommand ; 1d0ad28b-F791-4f5a-A60F-0e32318367b7 |ID d’application AAD =...|

#### <a name="example-single-step-purge"></a>Exemple : vidage en une seule étape

Pour déclencher une purge dans un scénario d’activation en une seule étape, exécutez la commande suivante :

```kusto
// Connect to the Data Management service
 #connect "https://ingest-[YourClusterName].[region].kusto.windows.net" 
 
.purge table MyTable records in database MyDatabase with (noregrets='true') <| where CustomerId in ('X', 'Y')
```

**Sortie**

| `OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mabdd |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Planifié | | | |0 |KE. RunCommand ; 1d0ad28b-F791-4f5a-A60F-0e32318367b7 |ID d’application AAD =...|

### <a name="cancel-purge-operation-command"></a>Commande Cancel purge Operation

Si nécessaire, vous pouvez annuler les demandes de vidage en attente.

> [!NOTE]
> Cette opération est destinée à des scénarios de récupération d’erreur. Il n’est pas garanti que l’opération aboutisse et ne doit pas faire partie d’un workflow opérationnel normal. Elle ne peut être appliquée qu’aux demandes dans la file d’attente (pas encore distribuées au nœud du moteur pour exécution). La commande est exécutée sur le point de terminaison Gestion des données.

**Syntaxe**

```kusto
 .cancel purge <OperationId>
```

**Exemple**

```kusto
 .cancel purge aa894210-1c60-4657-9d21-adb2887993e1
```

**Sortie**

La sortie de cette commande est identique à la sortie de la commande’Show purges *OperationId*', indiquant l’État mis à jour de l’annulation de l’opération de vidage. Si la tentative réussit, l’état de l’opération est mis à jour avec la valeur `Abandoned` . Dans le cas contraire, l’état de l’opération n’est pas modifié. 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mabdd |MyTable |2019-01-20 11:41:05.4391686 |00:00:00.1406211 |2019-01-20 11:41:05.4391686 | |Abandonné | | | |0 |KE. RunCommand ; 1d0ad28b-F791-4f5a-A60F-0e32318367b7 |ID d’application AAD =...

## <a name="track-purge-operation-status"></a>État de l’opération de purge des suivis 

> [!NOTE]
> Les opérations de purge peuvent être suivies à l’aide de la commande [Show purges](#show-purges-command) , exécutée sur le point de terminaison gestion des données https://ingest- [YourClusterName]. [ Region]. Kusto. Windows. net.

Status = 'Completed’indique la réussite de la première phase de l’opération de vidage, c’est-à-dire que les enregistrements sont supprimés de manière réversible et ne sont plus disponibles pour l’interrogation. Les clients ne sont pas censés suivre et vérifier la fin de la deuxième phase (suppression définitive). Cette phase est surveillée en interne par Azure Explorateur de données.

### <a name="show-purges-command"></a>Afficher les purges, commande

`Show purges` la commande affiche l’état de l’opération de purge en spécifiant l’ID d’opération dans la période demandée. 

```kusto
.show purges <OperationId>
.show purges [in database <DatabaseName>]
.show purges from '<StartDate>' [in database <DatabaseName>]
.show purges from '<StartDate>' to '<EndDate>' [in database <DatabaseName>]
```

| Propriétés  |Description  |Obligatoire/facultatif|
|---------|------------|-------|
|`OperationId `   |      L’ID d’opération de Gestion des données généré après l’exécution de la phase unique ou de la deuxième phase.   |Obligatoire
|`StartDate`    |   Limite de temps inférieure pour les opérations de filtrage. En cas d’omission, la valeur par défaut est 24 heures avant l’heure actuelle.      |Facultatif
|`EndDate`    |  Limite de temps supérieure pour les opérations de filtrage. En cas d’omission, la valeur par défaut est l’heure actuelle.       |Facultatif
|`DatabaseName`    |     Nom de la base de données pour filtrer les résultats.    |Facultatif

> [!NOTE]
> L’État ne sera fourni que sur les bases de données pour lesquelles le client dispose d’autorisations d' [administrateur de base de données](../management/access-control/role-based-authorization.md) .

**Exemples**

```kusto
.show purges
.show purges c9651d74-3b80-4183-90bb-bbe9e42eadc4
.show purges from '2018-01-30 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00'
.show purges from '2018-01-30 12:00' to '2018-02-25 12:00' in database MyDatabase
```

**Sortie** 

|`OperationId` |`DatabaseName` |`TableName` |`ScheduledTime` |`Duration` |`LastUpdatedOn` |`EngineOperationId` |`State` |`StateDetails` |`EngineStartTime` |`EngineDuration` |`Retries` |`ClientRequestId` |`Principal`
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|c9651d74-3b80-4183-90bb-bbe9e42eadc4 |Mabdd |MyTable |2019-01-20 11:41:05.4391686 |00:00:33.6782130 |2019-01-20 11:42:34.6169153 |a0825d4d-6b0f-47f3-a499-54ac5681ab78 |Effectué |Purge terminée avec succès (artefacts de stockage en attente de suppression) |2019-01-20 11:41:34.6486506 |00:00:04.4687310 |0 |KE. RunCommand ; 1d0ad28b-F791-4f5a-A60F-0e32318367b7 |ID d’application AAD =...

* `OperationId` : ID d’opération DM renvoyé lors de l’exécution de la purge. 
* `DatabaseName`* *-nom de la base de données (sensible à la casse). 
* `TableName` -nom de la table (sensible à la casse). 
* `ScheduledTime` -heure de l’exécution de la commande de vidage pour le service DM. 
* `Duration` -durée totale de l’opération de vidage, y compris le temps d’attente de la file d’attente DM d’exécution. 
* `EngineOperationId` : ID d’opération de la purge réelle en cours d’exécution dans le moteur. 
* `State` -purge de l’État, peut prendre l’une des valeurs suivantes : 
    * `Scheduled` -l’exécution de l’opération de vidage est planifiée. Si la tâche reste planifiée, il y a probablement un backlog d’opérations de vidage. Consultez [purger les performances](#purge-performance) pour effacer ce Backlog. Si une opération de vidage échoue sur une erreur temporaire, elle est retentée par le DM et définie à nouveau planifiée (de sorte que vous pouvez voir une opération transition de planifié à en cours, puis revenir à planifié).
    * `InProgress` -l’opération de vidage est en cours dans le moteur. 
    * `Completed` -purge terminée avec succès.
    * `BadInput` -échec de la purge en cas d’entrée incorrecte et ne sera pas retenté. Cet échec peut être dû à différents problèmes, tels qu’une erreur de syntaxe dans le prédicat, un prédicat non conforme pour les commandes de vidage, une requête qui dépasse les limites (par exemple, plus de 1 million d’entités dans un `externaldata` opérateur ou plus de 64 Mo de taille totale des requêtes étendues) et les erreurs 404 ou 403 pour les `externaldata` objets BLOB.
    * `Failed` -échec de la purge et aucune nouvelle tentative n’est effectuée. Cet échec peut se produire si l’opération attendait trop longtemps dans la file d’attente (plus de 14 jours), en raison d’un backlog d’autres opérations de vidage ou d’un certain nombre de défaillances qui dépassent la limite de tentatives. Cette dernière déclenchera une alerte de surveillance interne et sera examinée par l’équipe Azure Explorateur de données. 
* `StateDetails` -Description de l’État.
* `EngineStartTime` : heure à laquelle la commande a été envoyée au moteur. S’il y a une grande différence entre cette heure et ScheduledTime, il y a généralement un backlog significatif des opérations de vidage et le cluster ne suit pas le rythme. 
* `EngineDuration` -heure de l’exécution de la purge réelle dans le moteur. Si la purge a été retentée plusieurs fois, il s’agit de la somme de toutes les durées d’exécution. 
* `Retries` -nombre de fois que l’opération a été retentée par le service DM en raison d’une erreur temporaire.
* `ClientRequestId` -ID d’activité du client de la demande DM purge. 
* `Principal` -identité de l’émetteur de commande de vidage.

## <a name="purging-an-entire-table"></a>Vidage d’une table entière

La purge d’une table consiste à supprimer la table et à la marquer comme purgée afin que le processus de suppression matérielle décrit dans le [processus de vidage](#purge-process) s’exécute sur celle-ci. La suppression d’une table sans la vider n’entraîne pas la suppression de tous ses artefacts de stockage. Ces artefacts sont supprimés en fonction de la stratégie de rétention matérielle initialement définie sur la table. La `purge table allrecords` commande est rapide et efficace et est préférable au processus de vidage des enregistrements, s’il est applicable à votre scénario. 

> [!NOTE]
> La commande est appelée en exécutant la commande [purger la table *TableName* allrecords](#purge-table-tablename-allrecords-command) sur le point de terminaison gestion des données https://ingest- [YourClusterName]. [ Region]. Kusto. Windows. net.

### <a name="purge-table-tablename-allrecords-command"></a>Purger la table *TableName* allrecords, commande

Similaire à la commande'[. purger les enregistrements de table ](#purge-table-tablename-records-command)', cette commande peut être appelée en mode de programmation (à une seule étape) ou manuel (en deux étapes).

1. Appel programmatique (étape unique) :

     **Syntaxe**

     ```kusto
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     .purge table [TableName] in database [DatabaseName] allrecords with (noregrets='true')
     ```

1. Appel humain (deux étapes) :

     **Syntaxe**

     ```kusto
   
     // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
     // Step #1 - retrieve a verification token (the table will not be purged until step #2 is executed)

     .purge table [TableName] in database [DatabaseName] allrecords

     // Step #2 - input the verification token to execute purge
     .purge table [TableName] in database [DatabaseName] allrecords with (verificationtoken='<verification token from step #1>')
     ```

    | Paramètres  |Description  |
    |---------|---------|
    | `DatabaseName`   |   Nom de la base de données.      |
    | `TableName`    |     Nom de la table.    |
    | `noregrets`    |     Si cette valeur est définie, déclenche une activation en une seule étape.    |
    | `verificationtoken`     |  Dans un scénario d’activation en deux étapes (non `noregrets` défini), ce jeton peut être utilisé pour exécuter la deuxième étape et valider l’action. Si `verificationtoken` n’est pas spécifié, il déclenche la première étape de la commande. Dans cette étape, un jeton est retourné pour revenir à la commande et effectuer une étape #2.|

#### <a name="example-two-step-purge"></a>Exemple : vidage en deux étapes

1. Pour démarrer la purge dans un scénario d’activation en deux étapes, exécutez l’étape #1 de la commande : 

    ```kusto
    // Connect to the Data Management service
     #connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 
     
    .purge table MyTable in database MyDatabase allrecords
    ```

    **Sortie**

    | `VerificationToken`|
    |---|
    | e43c7184ed22f4f23c7a9d7b124d196be2e570096987e5baadf65057fa65736b|

1.  Pour effectuer une purge dans un scénario d’activation en deux étapes, utilisez le jeton de vérification retourné à partir de l’étape #1 pour exécuter l’étape #2 :

    ```kusto
    .purge table MyTable in database MyDatabase allrecords 
    with (verificationtoken='eyJTZXJ2aWNlTmFtZSI6IkVuZ2luZS1pdHNhZ3VpIiwiRGF0YWJhc2VOYW1lIjoiQXp1cmVTdG9yYWdlTG9ncyIsIlRhYmxlTmFtZSI6IkF6dXJlU3RvcmFnZUxvZ3MiLCJQcmVkaWNhdGUiOiIgd2hlcmUgU2VydmVyTGF0ZW5jeSA9PSAyNSJ9')
    ```
    
    La sortie est la même que la sortie de la commande'. Show tables' (retournée sans la table purgée).

    **Sortie**

    |  TableName|nom_base_de_données|Dossier|DocString
    |---|---|---|---
    |  OtherTable|Mabdd|---|---


#### <a name="example-single-step-purge"></a>Exemple : vidage en une seule étape

Pour déclencher une purge dans un scénario d’activation en une seule étape, exécutez la commande suivante :

```kusto
// Connect to the Data Management service
#connect "https://ingest-[YourClusterName].[Region].kusto.windows.net" 

.purge table MyTable in database MyDatabase allrecords with (noregrets='true')
```

La sortie est la même que la sortie de la commande'. Show tables' (retournée sans la table purgée).

**Sortie**

|TableName|nom_base_de_données|Dossier|DocString
|---|---|---|---
|OtherTable|Mabdd|---|---

