---
title: Traiter des données en double dans Azure Data Explorer
description: Cette rubrique décrit différentes approches du traitement des données en double en lien avec l’utilisation d’Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 12/19/2018
ms.openlocfilehash: ef71c978681c9e355e82791d4df3ad7da8db66f7
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874696"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Traiter des données en double dans Azure Data Explorer

Les appareils qui envoient des données dans le cloud gèrent un cache local des données. Selon la taille des données, le cache local peut stocker des données pendant plusieurs jours ou même plusieurs mois. Vous souhaitez protéger vos bases de données analytiques des appareils défectueux qui renvoient les données mises en cache et entraînent une duplication des données dans la base de données analytique. Cette rubrique décrit les bonnes pratiques pour la gestion des données en double pour ces types de scénarios.

La meilleure solution pour la duplication des données est d’empêcher cette duplication. Si possible, corrigez le problème plus tôt dans le pipeline de données, ce qui permet d’économiser les coûts associés au déplacement des données dans le pipeline de données et évite de consommer des ressources pour copier des données en double ingérées par le système. Cependant, dans les situations où le système source ne peut pas être modifié, il existe différentes façons de traiter ce scénario.

## <a name="understand-the-impact-of-duplicate-data"></a>Comprendre l’impact des données en double

Surveillez le pourcentage de données en double. Une fois que le pourcentage de données en double est déterminé, vous pouvez analyser l’étendue du problème et de l’impact sur l’activité, puis choisir la solution appropriée.

Exemple de requête pour identifier le pourcentage d’enregistrements en double :

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Solutions pour la gestion des données en double

### <a name="solution-1-dont-remove-duplicate-data"></a>Solution 1 : Ne pas supprimer pas les données en double

Comprenez les exigences de votre activité et la possibilité de tolérer la présence de données en double. Certains jeux de données peuvent s’accommoder d’un certain pourcentage de données en double. Si les données en double n’ont pas d’impact majeur, vous pouvez ignorer leur présence. L’avantage de ne pas supprimer les données en double est qu’aucune charge supplémentaire ne pèse sur le processus d’ingestion ou sur les performances des requêtes.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Solution 2 : Gérer les lignes en double lors des requêtes

Une autre option consiste à éliminer par filtrage les lignes en double dans les données lors des requêtes. La fonction d’agrégation [`arg_max()`](kusto/query/arg-max-aggfunction.md) peut être utilisée pour éliminer par filtrage les enregistrements en double et retourner le dernier enregistrement en fonction de l’horodatage (ou d’une autre colonne). L’avantage de cette méthode est une ingestion plus rapide, dans la mesure où la déduplication se produit au moment des requêtes. En outre, tous les enregistrements (y compris les doublons) sont disponibles pour l’audit et la résolution des problèmes. L’inconvénient de l’utilisation de la fonction `arg_max` est la durée plus longue des requêtes et la charge supplémentaire sur l’UC chaque fois que des données sont recherchées. Selon la quantité de données interrogées, cette solution peut devenir non fonctionnelle ou gourmande en mémoire, et nécessiter de passer à d’autres options.

Dans l’exemple suivant, nous interrogeons le dernier enregistrement ingéré pour un ensemble de colonnes qui déterminent l’unicité des enregistrements :

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Cette requête peut également être placée à l’intérieur d’une fonction, au lieu d’interroger directement la table :

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Solution 3 : Filtrer les doublons lors du processus d’ingestion

Une autre solution consiste à filtrer les doublons pendant le processus d’ingestion. Le système ignore les données en double lors de l’ingestion dans les tables Kusto. Les données sont ingérées dans une table intermédiaire, puis copiées dans une autre table après la suppression des lignes en double. L’avantage de cette solution est que les performances des requêtes sont considérablement meilleures par rapport à la solution précédente. Les inconvénients sont des temps d’ingestion accrus et des coûts supplémentaires de stockage des données. En plus, cette solution ne fonctionne que si les doublons ne sont pas ingérés simultanément. S’il existe plusieurs ingestions simultanées contenant des enregistrements en double, elles peuvent toutes être ingérées, car le processus de déduplication ne trouvera pas d’enregistrements correspondants existant dans la table.    

L’exemple suivant décrit cette méthode :

1. Créez une autre table avec le même schéma :

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Créez une fonction permettant d’éliminer par filtrage les enregistrements en double via une antijointure des nouveaux enregistrements avec ceux reçus précédemment.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Les jointures sont des opérations consommant de l’UC et ajoutent une charge supplémentaire sur le système.

1. Définissez une [stratégie de mise à jour](kusto/management/update-policy.md) sur la table `DeviceEventsUnique`. La stratégie de mise à jour est activée quand de nouvelles données entrent dans la table `DeviceEventsAll`. Le moteur Kusto exécute automatiquement la fonction quand de nouvelles [étendues](kusto/management/extents-overview.md) sont créées. Le traitement est limité aux données nouvellement créées. La commande suivante assemble la table source (`DeviceEventsAll`), la table de destination (`DeviceEventsUnique`) et la fonction `RemoveDuplicatesDeviceEvents` pour créer la stratégie de mise à jour.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > La stratégie de mise à jour allonge la durée de l’ingestion, dans la mesure où les données sont filtrées lors de l’ingestion, puis ingérées deux fois (dans la table `DeviceEventsAll` et dans la table `DeviceEventsUnique`).

1. (Facultatif) Définissez une durée de conservation des données inférieure sur la table `DeviceEventsAll` afin d’éviter de stocker des copies des données. Choisissez le nombre de jours en fonction du volume de données et de la durée pendant laquelle vous voulez conserver les données pour résoudre d’éventuels problèmes. Vous pouvez la définir sur `0d` jours de conservation pour réduire les coûts et améliorer les performances, car les données ne sont pas chargées dans le stockage.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Résumé

La déduplication des données peut être gérée de plusieurs façons. Évaluez les options avec soin, en tenant compte des prix et des performances pour déterminer la méthode correcte pour votre entreprise.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)
