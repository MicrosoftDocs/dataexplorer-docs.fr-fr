---
title: 'Tutoriel : Requêtes Kusto dans Azure Data Explorer et Azure Monitor'
description: Ce tutoriel explique comment utiliser des requêtes dans le langage de requête Kusto pour répondre aux besoins de requête courants dans Azure Data Explorer et Azure Monitor.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 930936bd9839730ccb0c438cc96a67334ea7ac71
ms.sourcegitcommit: 64b7b320875950dfee8eb1a23d36aa95e27d7297
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98207803"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>Tutoriel : Utiliser des requêtes Kusto dans Azure Data Explorer et Azure Monitor

::: zone pivot="azuredataexplorer"

La meilleure façon de se familiariser avec le langage de requête Kusto consiste à examiner certaines requêtes de base pour se faire une idée du langage. Nous vous recommandons d’utiliser une [base de données avec des exemples de données](https://help.kusto.windows.net/Samples). Les requêtes présentées dans ce tutoriel doivent s’exécuter sur cette base de données. La table `StormEvents` dans l’exemple de base de données fournit des informations sur les tempêtes survenues aux États-Unis.

## <a name="count-rows"></a>Compter les lignes

Notre exemple de base de données contient une table nommée `StormEvents`. Pour déterminer la taille de la table, nous allons canaliser son contenu vers un opérateur qui compte simplement les lignes que contient la table. 

*Note sur la syntaxe* : une requête est une source de données (généralement un nom de table), éventuellement suivie d’une ou plusieurs paires de caractères de canalisation (barre verticale) et d’un opérateur tabulaire.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

Voici le format :

|Nombre|
|-----|
|59066|
    
Pour plus d’informations, consultez [opérateur count](./countoperator.md).

## <a name="select-a-subset-of-columns-project"></a>Sélectionnez un sous-ensemble de colonnes : *projet*

Utilisez le [projet](./projectoperator.md) pour choisir uniquement les colonnes souhaitées. Reportez-vous à l’exemple suivant qui utilise à la fois le [projet](./projectoperator.md) et les opérateurs [take](./takeoperator.md).

## <a name="filter-by-boolean-expression-where"></a>Filtrez par expression booléenne : *where*

Voyons uniquement les événements `flood` survenus en `California` au cours du mois de février 2007 :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

Voici le format :

|StartTime|EndTime|État|Type d’événement|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|CALIFORNIE|Crue|Un système frontal se déplaçant dans la vallée méridionale de San Joaquin a entraîné de brefs épisodes de fortes précipitations dans l’ouest du comté de Kern aux premières heures de la matinée du 19. Des inondations mineures ont été signalées sur la route nationale 166 près de Taft.|

## <a name="show-n-rows-take"></a>Afficher *n* lignes : *take*

Voyons quelques données. Qu’est-ce qu’un échantillon aléatoire de cinq lignes ?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

Voici le format :

|StartTime|EndTime|Type d’événement|État|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Fortes précipitations|FLORIDE|Jusqu’à 228 mm de pluie ont chuté en 24 heures dans différentes parties de la zone côtière du comté de Volusia.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Tornade|FLORIDE|Une tornade a touché la ville d’Eustis à l’extrémité nord du lac West Crooked. La tornade s’est rapidement intensifiée pour atteindre la force EF1 alors qu’elle se déplaçait vers le nord-nord-ouest en traversant Eustis. Elle a laissé une empreinte d’une longueur d’environ 3 km et d’une largeur maximale de 275 mètres.  Elle a détruit 7 maisons. Vingt-sept maisons ont subi des dommages importants et 81 autres des dommages mineurs. Il n’y a eu aucun blessé grave et les dommages matériels sont évalués à 6,2 millions de dollars.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Trombe marine|ATLANTIQUE SUD|Une trombe s’est formée dans l’Atlantique au sud-est de Melbourne Beach et s’est brièvement déplacée vers le littoral.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Vent d’orage|MISSISSIPPI|De nombreux arbres de grande taille sont tombés, dont certains sur des lignes électriques. Les dégâts se sont produits dans l’est du comté d’Adams.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Vent d’orage|GEORGIE|La dépêche du comté a rapporté que plusieurs arbres sont tombés le long de Quincey Batten Loop, près de la route nationale 206. Le coût de leur enlèvement a été estimé.|

Mais l’opérateur [take](./takeoperator.md) affiche les lignes du tableau sans ordre particulier. Alors, trions-les ([limit](./takeoperator.md) est un alias de [take](./takeoperator.md) et a le même effet).

## <a name="order-results-sort-top"></a>Ordonner les résultats : *sort*, *top*

* *Note sur la syntaxe* : certains opérateurs ont des paramètres introduits par des mots clés tels que `by`.
* Dans l’exemple suivant, `desc` affiche les résultats dans l’ordre décroissant et `asc` dans l’ordre croissant.

Afficher les *n* premières lignes, ordonnées sur une colonne spécifique :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

Voici le format :

|StartTime|EndTime|Type d’événement|État|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête hivernale|MICHIGAN|Ces fortes chutes de neige se sont poursuivies jusqu’aux petites heures du matin le jour de l’an.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête hivernale|MICHIGAN|Ces fortes chutes de neige se sont poursuivies jusqu’aux petites heures du matin le jour de l’an.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête hivernale|MICHIGAN|Ces fortes chutes de neige se sont poursuivies jusqu’aux petites heures du matin le jour de l’an.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Vent fort|CALIFORNIE|Des vents du nord au nord-est avec des rafales à environ 93 km par heure ont été signalés dans les montagnes du comté de Ventura.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Vent fort|CALIFORNIE|Le capteur RAWS de Warm Springs a signalé des vents du nord avec des rafales à 93 km par heure.|

Vous pouvez obtenir le même résultat en utilisant les opérateurs [sort](./sortoperator.md), puis [take](./takeoperator.md) :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>Calculer des colonnes dérivées : *extend*

Créer une colonne en calculant une valeur dans chaque ligne :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

Voici le format :

|StartTime|EndTime|Duration|Type d’événement|État|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Fortes précipitations|FLORIDE|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Tornade|FLORIDE|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Trombe marine|ATLANTIQUE SUD|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Vent d’orage|MISSISSIPPI|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Vent d’orage|GEORGIE|

Il est possible de réutiliser un nom de colonne et d’attribuer un résultat de calcul à la même colonne.

Exemple :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

Voici le format :

|x|y|
|---|---|
|3|1|

Les [expressions scalaires](./scalar-data-types/index.md) peuvent inclure tous les opérateurs usuels (`+`, `-`, `*`, `/`, `%`), et une série de fonctions utiles sont disponibles.

## <a name="aggregate-groups-of-rows-summarize"></a>Agréger des groupes de lignes : *summarize*

Compter le nombre d’événements qui se produisent dans chaque État :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

L’opérateur [summarize](./summarizeoperator.md) regroupe les lignes qui ont les mêmes valeurs dans la clause `by`, puis utilise une fonction d’agrégation (par exemple, `count`) pour combiner chaque groupe dans une seule ligne. Dans ce cas, il y a une ligne pour chaque État et une colonne pour le nombre de lignes dans cet État.

Une série de [fonctions d’agrégation](./summarizeoperator.md#list-of-aggregation-functions) sont disponibles. Vous pouvez utiliser plusieurs fonctions d’agrégation dans un seul opérateur `summarize` pour produire plusieurs colonnes calculées. Par exemple, nous pourrions obtenir le nombre de tempêtes dans chaque État, ainsi que la somme des tempêtes d’un type unique par État. Ensuite, nous pourrions utiliser l’opérateur [top](./topoperator.md) pour obtenir les États les plus touchés par les tempêtes :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Voici le format :

|État|StormCount|TypeOfStorms|
|---|---|---|
|TEXAS|4701|27|
|KANSAS|3166|21|
|IOWA|2337|19|
|ILLINOIS|2022|23|
|MISSOURI|2016|20|

Dans les résultats d’un opérateur `summarize` :

* Chaque colonne est nommée dans `by`.
* Chaque expression calculée a une colonne.
* Chaque combinaison de valeurs `by` comporte une ligne.

## <a name="summarize-by-scalar-values"></a>Résumer en fonction de valeurs scalaires

Vous pouvez utiliser des valeurs scalaires (numériques, de temps ou d’intervalle) dans la clause `by`, mais vous voudrez les mettre dans des classes en utilisant la fonction [bin()](./binfunction.md) :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

La requête réduit tous les horodatages à des intervalles d’un jour :

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

La fonction [bin()](./binfunction.md) est identique à la fonction [floor()](./floorfunction.md) dans de nombreux langages. Elle réduit simplement chaque valeur au multiple le plus proche du modulo que vous fournissez, afin que l’opérateur [summarize](./summarizeoperator.md) puisse attribuer les lignes à des groupes.

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>Afficher un graphique ou une table : *render*

Vous pouvez projeter deux colonnes et les utiliser comme axes des abscisses et des ordonnées d’un graphique :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="Capture d’écran montrant un histogramme du nombre d’événements de tempête par État.":::

Bien que nous ayons supprimé `mid` dans l’opération `project`, nous en avons encore besoin si nous voulons que le graphique affiche les États dans cet ordre.

Strictement parlant, l’opérateur `render` est une fonctionnalité du client plutôt qu’une partie du langage de requête. Toutefois, il est intégré dans le langage et est utile pour visionner vos résultats.


## <a name="timecharts"></a>Graphiques temporels

Revenons aux compartiments numériques et affichons une série chronologique :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="Capture d’écran d’un graphique en courbes d’événements compartimentés par heure.":::

## <a name="multiple-series"></a>Séries multiples

Utilisez plusieurs valeurs dans une clause `summarize by` afin de créer une ligne distincte pour chaque combinaison de valeurs :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="Capture d’écran montrant un nombre de tables par source.":::

Ajoutez simplement le terme `render` à l’exemple précédent : `| render timechart`.

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="Capture d’écran montrant un nombre de graphiques en courbes par source.":::

Notez que l’opérateur `render timechart` utilise la première colonne comme axe des abscisses, puis affiche les autres colonnes sous forme de courbes distinctes.

## <a name="daily-average-cycle"></a>Cycle moyen quotidien

Comment l’activité varie-t-elle au cours d’une journée moyenne ?

Comptez les événements en fonction du modulo de temps d’un jour, compartimentés en heures. Ici, nous utilisons l’opérateur `floor` au lieu de l’opérateur `bin` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="Capture d’écran montrant un nombre de graphiques temporels par heure.":::

Actuellement, l’opérateur `render` n’étiquette pas les durées correctement, mais nous pourrions utiliser l’opérateur `| render columnchart` à la place :

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="Capture d’écran montrant un nombre d’histogrammes par heure.":::

## <a name="compare-multiple-daily-series"></a>Comparer plusieurs séries quotidiennes

Comment l’activité varie-t-elle au cours de la journée dans les différents États ?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="Capture d’écran d’un graphique temporel par heure et État.":::

Divisez par `1h` pour transformer l’axe des abscisses en un nombre d’heures au lieu d’une durée :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="Capture d’écran montrant un histogramme par heure et par État.":::

## <a name="join-data-types"></a>Joindre des types de données

Comment trouver deux types d’événements spécifiques et l’État dans lequel chacun s’est produit ?

Vous pouvez extraire des événements de tempête avec le premier `EventType` et le deuxième `EventType`, puis joindre les deux jeux sur `State` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="Capture d’écran montrant comment joindre les événements de foudre et d’avalanche.":::

## <a name="user-session-example-of-join"></a>Exemple de session utilisateur de l’opérateur *join*

Cette section n’utilise pas le tableau `StormEvents`.

Supposons que vous avez des données qui incluent des événements marquant le début et la fin de chaque session utilisateur avec un ID unique pour chaque session. 

Comment déterminer la durée de chaque session utilisateur ?

Vous pouvez utiliser l’opérateur `extend` pour fournir un alias pour les deux horodatages, puis calculer la durée de la session :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
Events
| where eventName == "session_started"
| project start_time = timestamp, stop_time, country, session_id
| join ( Events
    | where eventName == "session_ended"
    | project stop_time = timestamp, session_id
    ) on session_id
| extend duration = stop_time - start_time
| project start_time, stop_time, country, duration
| take 10
```

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="Capture d’écran d’une table de résultats pour une extension de session utilisateur.":::

Il est recommandé d’utiliser `project` pour sélectionner uniquement les colonnes dont vous avez besoin avant d’effectuer la jointure. Dans les mêmes clauses, renommez la colonne `timestamp`.

## <a name="plot-a-distribution"></a>Tracer une distribution

En revenant au tableau `StormEvents`, combien y a-t-il de tempêtes de longueurs différentes ?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m)
| sort by duration asc
| render timechart
```

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="Capture d’écran des résultats de graphique temporel pour le nombre d’événements par durée.":::

Vous pouvez également utiliser l’opérateur `| render columnchart` :

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="Capture d’écran d’un histogramme pour le graphique temporel du nombre d’événements par durée.":::

## <a name="percentiles"></a>Centiles

Quelles plages de durées trouvons-nous dans les différents pourcentages de tempêtes ?

Pour obtenir ces informations, utilisez la requête précédente, mais remplacez `render` par :

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Dans ce cas, comme nous n’avons pas utilisé de clause `by`, la sortie est une ligne unique :

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="Capture d’écran d’une table de résultats pour résumer les centiles par durée.":::

Nous pouvons voir dans la sortie que :

* 5 % des tempêtes ont une durée inférieure à 5 minutes.
* 50 % des tempêtes ont duré moins d’une heure et 25 minutes.
* 95 % des tempêtes ont duré au moins deux heures et 50 minutes.

Pour obtenir une répartition distincte pour chaque État, utilisez la colonne `state` séparément avec les deux opérateurs `summarize` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend  duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count()
    by bin(duration, 5m), State
| sort by duration asc
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="Table résumant les centiles de durée par État.":::

## <a name="assign-a-result-to-a-variable-let"></a>Attribuer un résultat à une variable : *let*

Utilisez l’opérateur [let](./letstatement.md) pour séparer les parties de l’expression de requête dans l’exemple d’utilisation de l’opérateur `join` précédent. Les résultats sont identiques :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let LightningStorms = 
    StormEvents
    | where EventType == "Lightning";
let AvalancheStorms = 
    StormEvents
    | where EventType == "Avalanche";
LightningStorms 
| join (AvalancheStorms) on State
| distinct State
```
> [!TIP]
> Dans Kusto Explorer, pour exécuter l’intégralité de la requête, n’ajoutez pas de lignes vides entre les différentes parties de la requête.

## <a name="combine-data-from-several-databases-in-a-query"></a>Combiner des données de plusieurs bases de données dans une requête

Dans la requête suivante, la table `Logs` doit se trouver dans votre base de données par défaut :

```kusto
Logs | where ...
```

Pour accéder à une table dans une base de données différente, utilisez la syntaxe suivante :

```kusto
database("db").Table
```

Par exemple, si vous avez des bases de données nommées `Diagnostics` et `Telemetry`, et souhaitez mettre en corrélation certaines données dans les deux tables, vous pourriez utiliser la requête suivante (en supposant que `Diagnostics` est votre base de données par défaut) :

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

Utilisez cette requête si votre base de données par défaut est `Telemetry`:

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Les deux requêtes précédentes supposent que les deux bases de données se trouvent dans le cluster auquel vous êtes actuellement connecté. Si la base de données `Telemetry` se trouvait dans un cluster nommé *TelemetryCluster.kusto.windows.net*, pour y accéder, utilisez la requête suivante :

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> Lorsque le cluster est spécifié, la base de données est obligatoire.

Pour plus d’informations sur la combinaison de données de plusieurs bases de données dans une requête, consultez [Requêtes de bases de données croisées](cross-cluster-or-database-queries.md).

## <a name="next-steps"></a>Étapes suivantes

- Consultez les exemples de code pour le [langage de requête Kusto](samples.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

La meilleure façon de se familiariser avec le langage de requête Kusto consiste à examiner certaines requêtes de base pour se faire une idée du langage. Ces requêtes sont similaires à celles utilisées dans le tutoriel Azure Data Explorer, mais elles utilisent à la place des données de tables communes dans un espace de travail Azure Log Analytics. 

Exécutez ces requêtes à l’aide de Log Analytics dans le portail Azure. Log Analytics est un outil que vous pouvez utiliser pour écrire des requêtes de journal. Utilisez les données de journal dans Azure Monitor, puis évaluez les résultats de requête de journal. Si vous n’êtes pas familiarisé avec Log Analytics, suivez le [Tutoriel Log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial).

Toutes les requêtes de ce tutoriel utilisent l’[environnement de démonstration Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade). Vous pouvez utiliser votre propre environnement, mais il se peut que vous ne disposiez pas de certaines des tables utilisées ici. Étant donné que les données de l’environnement de démonstration ne sont pas statiques, les résultats de vos requêtes pourraient différer légèrement de ceux présentés ici.


## <a name="count-rows"></a>Compter les lignes

La table [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) contient des données de performances collectées par des insights telles qu’Azure Monitor pour machines virtuelles et Azure Monitor pour conteneurs. Pour déterminer la taille de la table, nous allons canaliser son contenu vers un opérateur qui compte simplement les lignes.

une requête est une source de données (généralement un nom de table), éventuellement suivie d’une ou plusieurs paires de caractères de canalisation (barre verticale) et d’un opérateur tabulaire. Dans ce cas, tous les enregistrements de la table `InsightsMetrics` sont retournés, puis envoyés à l’[opérateur count](./countoperator.md). L’opérateur `count` affiche les résultats, car il est la dernière commande de la requête.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

Voici le format :

|Nombre|
|-----|
|1 263 191|
    

## <a name="filter-by-boolean-expression-where"></a>Filtrer par expression booléenne : *where*

La table [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) contient des entrées du journal d’activité Azure qui fournit une insight de tous les événements au niveau de l’abonnement ou du groupe d’administration qui se sont produits dans Azure. Voyons uniquement les entrées `Critical` durant une semaine spécifique.

L’opérateur [where](/azure/data-explorer/kusto/query/whereoperator) est courant dans le langage de requête Kusto. L’opérateur `where` filtre une table sur des lignes correspondant à des critères spécifiques. L’exemple suivant utilise plusieurs commandes. Tout d’abord, la requête récupère tous les enregistrements de la table. Ensuite, elle filtre les données uniquement pour les enregistrements qui se trouvent dans l’intervalle de temps. Enfin, elle filtre ces résultats uniquement pour les enregistrements qui ont un niveau `Critical`.

> [!NOTE]
> En plus de spécifier un filtre dans votre requête à l’aide de la colonne `TimeGenerated`, vous pouvez spécifier l’intervalle de temps dans Log Analytics. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope).

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur where.":::

## <a name="select-a-subset-of-columns-project"></a>Sélectionnez un sous-ensemble de colonnes : *projet*

Utilisez l’opérateur [project](./projectoperator.md) pour inclure uniquement les colonnes souhaitées. En nous basant sur l’exemple précédent, limitons la sortie à certaines colonnes :

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur project.":::

## <a name="show-n-rows-take"></a>Afficher *n* lignes : *take*

[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) contient des données de surveillance pour des réseaux virtuels Azure. Utilisons l’opérateur [take](./takeoperator.md) pour examiner dix lignes aléatoires de cette table. L’opérateur [take](./takeoperator.md) affiche un certain nombre de lignes d’une table sans ordre particulier :

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur take.":::

## <a name="order-results-sort-top"></a>Ordonner les résultats : *sort*, *top*

Au lieu d’enregistrements aléatoires, nous pouvons retourner les cinq derniers enregistrements en commençant par trier par heure :

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

Vous pouvez obtenir ce comportement précis en utilisant à la place l’opérateur [top](./topoperator.md) : 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur top.":::

## <a name="compute-derived-columns-extend"></a>Calculer des colonnes dérivées : *extend*

L’opérateur [extend](./projectoperator.md) est similaire à l’opérateur [project](./projectoperator.md), mais il ajoute des colonnes à l’ensemble de colonnes au lieu de les remplacer. Vous pouvez utiliser les deux opérateurs pour créer une colonne basée sur un calcul effectué sur chaque ligne.

La table [Perf](/azure/azure-monitor/reference/tables/perf) contient des données de performances collectées à partir des machines virtuelles qui exécutent l’agent Log Analytics. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur extend.":::

## <a name="aggregate-groups-of-rows-summarize"></a>Agréger des groupes de lignes : *summarize*

L’opérateur [summarize](./summarizeoperator.md) regroupe les lignes qui ont les mêmes valeurs dans la clause `by`. Ensuite, il utilise une fonction d’agrégation comme `count` pour combiner chaque groupe dans une seule ligne. Une série de [fonctions d’agrégation](./summarizeoperator.md#list-of-aggregation-functions) sont disponibles. Vous pouvez utiliser plusieurs fonctions d’agrégation dans un seul opérateur `summarize` pour produire plusieurs colonnes calculées. 

La table [SecurityEvent](/azure/azure-monitor/reference/tables/securityevent) contient des événements de sécurité tels que les ouvertures de session et les processus qui ont démarré sur les machines surveillées. Vous pouvez compter le nombre d’événements de chaque niveau qui se sont produits sur chaque ordinateur. Dans cet exemple, une ligne est produite pour chaque combinaison d’ordinateur et de niveau. Une colonne contient le nombre d’événements.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur summarize count.":::

## <a name="summarize-by-scalar-values"></a>Résumer en fonction de valeurs scalaires

Vous pouvez agréger par valeurs scalaires telles que des nombres et des valeurs de temps, mais vous devez utiliser la fonction [bin()](./binfunction.md) pour regrouper les lignes dans des jeux de données distincts. Par exemple, si vous agrégez par `TimeGenerated`, vous obtenez une ligne pour presque chaque valeur de temps. Utilisez la fonction `bin()` pour consolider ces valeurs dans une heure ou un jour.

La table [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) contient des données de performances collectées par des insights telles qu’Azure Monitor pour machines virtuelles et Azure Monitor pour conteneurs. La requête suivante affiche l’utilisation moyenne horaire du processeur pour plusieurs ordinateurs :

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur avg.":::

## <a name="display-a-chart-or-table-render"></a>Afficher un graphique ou une table : *render*

L’opérateur [render](./renderoperator.md?pivots=azuremonitor) spécifie le mode d’affichage de la sortie de la requête. Log Analytics affiche la sortie sous la forme d’une table par défaut. Vous pouvez sélectionner différents types de graphiques après l’exécution de la requête. L’opérateur `render` est utile à inclure dans des requêtes dans lesquelles un type de graphique spécifique est généralement préféré.

L’exemple suivant montre l’utilisation moyenne horaire du processeur pour un ordinateur unique. Il affiche la sortie sous la forme d’un graphique temporel.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur render.":::

## <a name="work-with-multiple-series"></a>Travailler avec plusieurs séries

Si vous utilisez plusieurs valeurs dans une clause `summarize by`, le graphique affiche une série distincte pour chaque jeu de valeurs :

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur render avec plusieurs séries.":::

## <a name="join-data-from-two-tables"></a>Joindre des données de deux tables

Que se passe-t-il si vous avez besoin de récupérer des données de deux tables dans une seule requête ? Vous pouvez utiliser l’opérateur [join](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) pour combiner des lignes de plusieurs tables dans un seul jeu de résultats. Chaque table doit avoir une colonne contenant une valeur correspondante afin que la jointure comprenne les lignes à faire correspondre.

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer) est une table qu’Azure Monitor utilise pour les machines virtuelles, afin de stocker des détails sur les machines virtuelles qu’il surveille. [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) contient des données de performances collectées à partir de ces machines virtuelles. Une valeur collectée dans *InsightsMetrics* est la mémoire disponible, mais pas le pourcentage de mémoire disponible. Pour calculer le pourcentage, nous avons besoin de la mémoire physique pour chaque machine virtuelle. Cette valeur est dans `VMComputer`.

L’exemple de requête suivant utilise une jointure pour effectuer ce calcul. L’opérateur [distinct](/azure/data-explorer/kusto/query/distinctoperator) est utilisé avec la table `VMComputer`, car des détails sont régulièrement collectés à partir de chaque ordinateur. Par conséquent, plusieurs lignes sont créées pour chaque ordinateur dans la table. Les deux tables sont jointes à l’aide de la colonne `Computer`. Une ligne est créée dans le jeu de résultats qui contient des colonnes des deux tables pour chaque ligne dans `InsightsMetrics`, avec une valeur dans `Computer` qui correspond à la même valeur dans la colonne `Computer` dans `VMComputer`.

```kusto
VMComputer
| distinct Computer, PhysicalMemoryMB
| join kind=inner (
    InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val
) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur join.":::

## <a name="assign-a-result-to-a-variable-let"></a>Attribuer un résultat à une variable : *let*

Utilisez l’opérateur [let](./letstatement.md) pour faciliter la lecture et la gestion des requêtes. Vous pouvez vous servir de cet opérateur pour attribuer les résultats d’une requête à une variable que vous pourrez utiliser ultérieurement. À l’aide de l’instruction `let`, la requête de l’exemple précédent peut être réécrite comme suit :

 
```kusto
let PhysicalComputer = VMComputer
    | distinct Computer, PhysicalMemoryMB;
    let AvailableMemory = 
InsightsMetrics
    | where Namespace == "Memory" and Name == "AvailableMB"
    | project TimeGenerated, Computer, AvailableMemoryMB = Val;
PhysicalComputer
| join kind=inner (AvailableMemory) on Computer
| project TimeGenerated, Computer, PercentMemory = AvailableMemoryMB / PhysicalMemoryMB * 100
```

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="Capture d’écran montrant les résultats de l’exemple d’utilisation de l’opérateur let.":::

## <a name="next-steps"></a>Étapes suivantes

- Consultez les exemples de code pour le [langage de requête Kusto](samples.md?pivots=azuremonitor).


::: zone-end
