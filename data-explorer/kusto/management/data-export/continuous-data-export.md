---
title: Exportation de données en continu-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’exportation continue de données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: yifats
ms.service: data-explorer
ms.topic: reference
ms.date: 08/03/2020
ms.openlocfilehash: 7f9465df4847a24a4877c8b1cb637ba1d7542db3
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342533"
---
# <a name="continuous-data-export-overview"></a>Vue d’ensemble de l’exportation continue de données

Cet article décrit l’exportation continue de données de Kusto vers une [table externe](../external-table-commands.md) avec une requête exécutée périodiquement. Les résultats sont stockés dans la table externe, qui définit la destination, comme le stockage d’objets BLOB Azure, et le schéma des données exportées. Ce processus garantit que tous les enregistrements sont exportés « exactement une fois », à quelques [exceptions près](#exactly-once-export). 

Pour activer l’exportation continue des données, [créez une table externe](../external-tables-azurestorage-azuredatalake.md#create-or-alter-external-table) , puis [créez une définition d’exportation continue](create-alter-continuous.md) pointant vers la table externe. 

> [!NOTE]
> Toutes les commandes d’exportation continues nécessitent des [autorisations d’administrateur de base de données](../access-control/role-based-authorization.md).

## <a name="continuous-export-guidelines"></a>Instructions d’exportation continue

* **Schéma de sortie**:
  * Le schéma de sortie de la requête d’exportation doit correspondre au schéma de la table externe vers laquelle vous exportez. 
* **Fréquence**:
  * L’exportation continue s’exécute en fonction de la période configurée pour celle-ci dans la `intervalBetweenRuns` propriété. La valeur recommandée pour cet intervalle est d’au moins plusieurs minutes, selon les latences que vous êtes prêt à accepter. L’intervalle de temps peut être inférieur à une minute, si le taux d’ingestion est élevé.
* **Distribution**:
  * La distribution par défaut dans l’exportation continue est `per_node` , dans laquelle tous les nœuds sont exportés simultanément. 
  * Ce paramètre peut être substitué dans les propriétés de la commande Continuous Export Create. Utilisez la `per_shard` distribution pour augmenter l’accès concurrentiel.
    > [!NOTE]
    > Cette distribution augmente la charge sur le (s) compte (s) de stockage et a un risque de toucher les limites de limitation. 
  * Utilisez `single` (ou `distributed` = `false` ) pour désactiver complètement la distribution. Ce paramètre peut considérablement ralentir le processus d’exportation continue et avoir un impact sur le nombre de fichiers créés lors de chaque itération d’exportation continue. 
* **Nombre de fichiers**:
  * Le nombre de fichiers exportés dans chaque itération d’exportation continue dépend de la façon dont la table externe est partitionnée. Pour plus d’informations, consultez [commande exporter vers une table externe](export-data-to-an-external-table.md#number-of-files). Chaque itération d’exportation continue écrit toujours dans de nouveaux fichiers et n’est jamais ajoutée à des fichiers existants. Par conséquent, le nombre de fichiers exportés dépend également de la fréquence d’exécution de l’exportation continue. Le paramètre Frequency est `intervalBetweenRuns` .
* **Emplacement** :
  * Pour de meilleures performances, le cluster Azure Explorateur de données et le ou les comptes de stockage doivent être colocalisés dans la même région Azure.
  * Si le volume de données exporté est important, il est recommandé de configurer plusieurs comptes de stockage pour la table externe, afin d’éviter la limitation du stockage. Consultez [exporter des données vers le stockage](export-data-to-storage.md#known-issues).

## <a name="exactly-once-export"></a>Exportation exacte

Pour garantir une exportation « exactement une fois », l’exportation continue utilise des [curseurs de base de données](../databasecursor.md). La [stratégie IngestionTime](../ingestiontime-policy.md) doit être activée sur toutes les tables référencées dans la requête qui doivent être traitées « exactement une fois » dans l’exportation. La stratégie est activée par défaut sur toutes les tables nouvellement créées.

La garantie d’exportation « exactement une fois » concerne uniquement les fichiers signalés dans la [commande Afficher les artefacts exportés](show-continuous-artifacts.md). L’exportation continue ne garantit pas que chaque enregistrement sera écrit une seule fois dans la table externe. Si une défaillance se produit après le début de l’exportation et que certains des artefacts ont déjà été écrits dans la table externe, la table externe peut contenir des doublons. Si une opération d’écriture a été abandonnée avant la fin, la table externe peut contenir des fichiers endommagés. Dans ce cas, les artefacts ne sont pas supprimés de la table externe, mais ils ne sont pas signalés dans la [commande Afficher les artefacts exportés](show-continuous-artifacts.md). La consommation des fichiers exportés à l’aide de `show exported artifacts command` ne garantit aucune duplication et aucune altération.

## <a name="export-to-fact-and-dimension-tables"></a>Exporter vers les tables de faits et de dimension

Par défaut, toutes les tables référencées dans la requête d’exportation sont supposées être des [tables de faits](../../concepts/fact-and-dimension-tables.md). Par conséquent, ils sont étendus au curseur de base de données. La syntaxe déclare explicitement quelles tables sont étendues (fait) et qui ne sont pas délimitées (dimension). `over`Pour plus d’informations, consultez le paramètre dans la [commande CREATE](create-alter-continuous.md) .

La requête d’exportation comprend uniquement les enregistrements joints depuis l’exécution de l’exportation précédente. La requête d’exportation peut contenir des [tables de dimension](../../concepts/fact-and-dimension-tables.md) dans lesquelles tous les enregistrements de la table de dimension sont inclus dans toutes les requêtes d’exportation. Lors de l’utilisation de jointures entre les tables de faits et de dimension en mode d’exportation continu, gardez à l’esprit que les enregistrements de la table de faits ne sont traités qu’une seule fois. Si l’exportation s’exécute alors que des enregistrements dans les tables de dimension sont absents pour certaines clés, les enregistrements des clés respectives sont ignorés ou incluent des valeurs NULL pour les colonnes de dimension dans les fichiers exportés. Le renvoi d’enregistrements manqués ou nuls varie selon que la requête utilise une jointure interne ou externe. La `forcedLatency` propriété dans la définition d’exportation continue peut être utile dans de tels cas, où les tables de faits et de dimensions sont ingérées en même temps pour les enregistrements correspondants.

> [!NOTE]
> L’exportation continue d’une seule table de dimension n’est pas prise en charge. La requête d’exportation doit inclure au moins une table de faits unique.

## <a name="exporting-historical-data"></a>Exportation de données historiques

L’exportation continue commence à exporter des données uniquement à partir du point de sa création. Les enregistrements ingérés avant cette heure doivent être exportés séparément à l’aide de la [commande d’exportation](export-data-to-an-external-table.md)non continue. 

Les données d’historique peuvent être trop volumineuses pour être exportées dans une seule commande d’exportation. Si nécessaire, partitionnez la requête en plusieurs lots plus petits. 

Pour éviter les doublons avec les données exportées par l’exportation continue, utilisez `StartCursor` retournée par la [commande afficher l’exportation continue](show-continuous-export.md) et Export enregistre uniquement `where cursor_before_or_at` la valeur de curseur. Par exemple :

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

## <a name="resource-consumption"></a>Consommation des ressources

* L’impact de l’exportation continue sur le cluster dépend de la requête exécutée par l’exportation continue. La plupart des ressources, telles que le processeur et la mémoire, sont consommées par l’exécution de la requête. 
* Le nombre d’opérations d’exportation qui peuvent s’exécuter simultanément est limité par la capacité d’exportation des données du cluster. Pour plus d’informations, consultez [limitation](../../management/capacitypolicy.md#throttling). Si le cluster ne dispose pas d’une capacité suffisante pour gérer toutes les exportations continues, d’autres démarreront en retard.
* La [commande show Commands-and-Queries](../commands-and-queries.md) peut être utilisée pour estimer la consommation des ressources. 
  * Filtrez sur `| where ClientActivityId startswith "RunContinuousExports"` pour afficher les commandes et les requêtes associées à l’exportation continue.

## <a name="limitations"></a>Limites

* L’exportation continue ne fonctionne pas pour les données ingérées à l’aide de l’ingestion de diffusion en continu. 
* L’exportation continue ne peut pas être configurée sur une table pour laquelle une [stratégie de sécurité au niveau des lignes](../../management/rowlevelsecuritypolicy.md) est activée.
* L’exportation continue n’est pas prise en charge pour les tables externes avec `impersonate` dans leurs [chaînes de connexion](../../api/connection-strings/storage.md).
* L’exportation continue ne prend pas en charge les appels entre plusieurs clusters et bases de données croisées.
* L’exportation continue n’est pas conçue pour diffuser en continu des données à partir d’Azure Explorateur de données. L’exportation continue s’exécute en mode distribué, où tous les nœuds sont exportés simultanément. Si la plage de données interrogée par chaque exécution est faible, la sortie de l’exportation continue serait un grand nombre de petits artefacts. Le nombre d’artefacts dépend du nombre de nœuds dans le cluster.
* Si les artefacts utilisés par l’exportation continue sont destinés à déclencher des notifications Event Grid, consultez la [section problèmes connus dans la documentation de Event Grid](../../../ingest-data-event-grid-overview.md#known-event-grid-issues).
