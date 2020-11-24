---
title: 'Didacticiel : requêtes Kusto dans Azure Explorateur de données & Azure Monitor'
description: Ce didacticiel explique comment utiliser des requêtes dans le langage de requête Kusto pour répondre aux besoins de requête courants dans Azure Explorateur de données et Azure Monitor.
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
ms.openlocfilehash: 8a47c51aa7924a28b27602056ea869bfd7a09936
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783720"
---
# <a name="tutorial-use-kusto-queries-in-azure-data-explorer-and-azure-monitor"></a>Didacticiel : utiliser des requêtes Kusto dans Azure Explorateur de données et Azure Monitor

::: zone pivot="azuredataexplorer"

La meilleure façon de se familiariser avec le langage de requête Kusto consiste à examiner certaines requêtes de base pour obtenir une « sensation » du langage. Nous vous recommandons d’utiliser une [base de données avec des exemples de données](https://help.kusto.windows.net/Samples). Les requêtes présentées dans ce didacticiel doivent s’exécuter sur cette base de données. La `StormEvents` table de l’exemple de base de données fournit des informations sur les tempêtes qui se sont produites dans le États-Unis.

## <a name="count-rows"></a>Compter les lignes

Notre exemple de base de données a une table appelée `StormEvents` . Pour déterminer la taille de la table, nous allons diriger son contenu dans un opérateur qui compte simplement les lignes dans la table. 

*Remarque relative* à la syntaxe : une requête est une source de données (généralement un nom de table), éventuellement suivie d’une ou plusieurs paires de caractères de barre verticale et d’un opérateur tabulaire.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

Voici le format :

|Count|
|-----|
|59066|
    
Pour plus d’informations, consultez [opérateur Count](./countoperator.md).

## <a name="select-a-subset-of-columns-project"></a>Sélectionner un sous-ensemble de colonnes : *projet*

Utilisez [Project](./projectoperator.md) pour choisir uniquement les colonnes de votre choix. Consultez l’exemple suivant, qui utilise à la fois le [projet](./projectoperator.md) et les opérateurs [Take](./takeoperator.md) .

## <a name="filter-by-boolean-expression-where"></a>Filtrer par expression booléenne : *Where*

Voyons uniquement `flood` les événements de la section `California` février-2007 :

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
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|France|Crue|Un système frontal qui évolue à travers le sud de la Joaquin de la vallée A introduit de courtes périodes de pluie lourde dans le comté de crénage occidental au cours des premières heures du 19. Une saturation mineure a été signalée sur l’autoroute d’État 166 près de Taft.|

## <a name="show-n-rows-take"></a>Afficher *n* lignes : *Take*

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
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Pluie lourde|Floride|Jusqu’à 9 pouces de pluie chutaient sur une période de 24 heures sur des parties du comté Volusia côtier.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Tornade|Floride|Tornade évoqué dans la ville de Eustis, à la fin du Nord de l’ouest du lac. La tornade s’est rapidement intensifiée sur la force EF1 à mesure qu’elle a été déplacée Nord-Nord-Ouest à Eustis. La piste se trouvait juste à moins de deux kilomètres et avait une largeur maximale de 300 mètres.  La tornade a détruit 7 maisons. Vingt sept maisons ont reçu des dommages majeurs et 81 maisons signalaient des dommages mineurs. Il n’y avait pas de blessures graves et les dommages liés aux propriétés ont été définis à $6,2 millions.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Waterspout|SUD DE L’ATLANTIQUE|Un Waterspout formé dans le sud-est de l’Atlantique de Melbourne Beach et placé brièvement vers la terre.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Vent d’orage|MISSISSIPPI|De nombreux arbres de grande taille ont été décomposés de lignes d’alimentation. Les dommages se sont produits dans le comté de l’est Adams.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Vent d’orage|Géorgie|La distribution de la région a rapporté que plusieurs arbres ont été relevés le long de la route Quincey-Loop à l’état près de la route 206. Le coût de la suppression de l’arborescence a été estimé.|

Mais [Take](./takeoperator.md) affiche les lignes de la table dans un ordre particulier, donc nous allons les trier. (la[limite](./takeoperator.md) est un alias pour [Take](./takeoperator.md) et a le même effet.)

## <a name="order-results-sort-top"></a>Résultats de la commande : *Trier*, *haut*

* *Remarque relative* à la syntaxe : certains opérateurs ont des paramètres introduits par des mots clés tels que `by` .
* Dans l’exemple suivant, `desc` Orders génère un ordre décroissant et `asc` trie les résultats dans l’ordre croissant.

Afficher les *n* premières lignes, classées par une colonne spécifique :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

Voici le format :

|StartTime|EndTime|Type d’événement|État|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête d’hiver|MICHIGAN|Cet événement lourd a continué au début des heures du matin le jour de la nouvelle année.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête d’hiver|MICHIGAN|Cet événement lourd a continué au début des heures du matin le jour de la nouvelle année.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête d’hiver|MICHIGAN|Cet événement lourd a continué au début des heures du matin le jour de la nouvelle année.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Vent élevé|France|Les vents du Nord vers le nord-est à environ 58 km/h sont signalés dans les montagnes du comté Ventura.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Vent élevé|France|Le capteur RAWS de ressorts à chaud a signalé un rafale des vents à 58 km/h.|

Vous pouvez obtenir le même résultat en utilisant l' [ordre de tri](./sortoperator.md), puis [effectuer](./takeoperator.md)les opérations suivantes :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="compute-derived-columns-extend"></a>Colonnes dérivées de calcul : *étendre*

Créez une nouvelle colonne en calculant une valeur dans chaque ligne :

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
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Pluie lourde|Floride|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Tornade|Floride|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Waterspout|SUD DE L’ATLANTIQUE|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Vent d’orage|MISSISSIPPI|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Vent d’orage|Géorgie|

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

Les [expressions scalaires](./scalar-data-types/index.md) peuvent inclure tous les opérateurs habituels ( `+` , `-` ,, `*` `/` , `%` ) et une plage de fonctions utiles sont disponibles.

## <a name="aggregate-groups-of-rows-summarize"></a>Agréger des groupes de lignes : *synthétiser*

Nombre d’événements se produisant dans chaque pays :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[résume](./summarizeoperator.md) les groupes ensemble de lignes qui ont les mêmes valeurs dans la `by` clause, puis utilise une fonction d’agrégation (par exemple, `count` ) pour combiner chaque groupe dans une ligne unique. Dans ce cas, il y a une ligne pour chaque État et une colonne pour le nombre de lignes dans cet État.

Une plage de [fonctions d’agrégation](./summarizeoperator.md#list-of-aggregation-functions) est disponible. Vous pouvez utiliser plusieurs fonctions d’agrégation dans un `summarize` opérateur pour produire plusieurs colonnes calculées. Par exemple, nous pourrions obtenir le nombre de tempêtes dans chaque État et également la somme d’un type unique de tempêtes par État. Ensuite, nous pourrions utiliser [Top](./topoperator.md) pour connaître les États les plus affectés à la Storm :

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

Dans les résultats d’un `summarize` opérateur :

* Chaque colonne est nommée dans `by` .
* Chaque expression calculée a une colonne.
* Chaque combinaison de `by` valeurs a une ligne.

## <a name="summarize-by-scalar-values"></a>Résumer en fonction de valeurs scalaires

Vous pouvez utiliser des valeurs scalaires (numériques, horaires ou d’intervalle) dans la `by` clause, mais vous souhaiterez placer les valeurs dans des emplacements à l’aide de la fonction [bin ()](./binfunction.md) :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

La requête réduit tous les horodateurs aux intervalles d’un jour :

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

[Bin ()](./binfunction.md) est identique à la fonction [Floor ()](./floorfunction.md) dans de nombreux langages. Elle réduit simplement chaque valeur au multiple le plus proche du modulo que vous fournissez, afin que la [synthèse](./summarizeoperator.md) puisse assigner les lignes aux groupes.

<a name="displaychartortable"></a>
## <a name="display-a-chart-or-table-render"></a>Afficher un graphique ou une table : *rendu*

Vous pouvez projeter deux colonnes et les utiliser comme axe des abscisses et axe des ordonnées d’un graphique :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="Capture d’écran montrant un histogramme du nombre d’événements Storm par État.":::

Bien que nous ayons supprimé l' `mid` `project` opération, nous en avons encore besoin si nous souhaitons que le graphique affiche les pays dans cet ordre.

Strictement parlant, `render` est une fonctionnalité du client au lieu d’une partie du langage de requête. Toutefois, il est intégré dans le langage et il est utile pour la vision de vos résultats.


## <a name="timecharts"></a>Graphiques temporels

En revenons aux emplacements numériques, nous affichons une série chronologique :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="Capture d’écran d’un graphique en courbes d’événements Binned (par heure.":::

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

Ajoutez simplement le `render` terme à l’exemple précédent : `| render timechart` .

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="Capture d’écran montrant un graphique en courbes nombre par source.":::

Notez que `render timechart` utilise la première colonne comme axe des abscisses, puis affiche les autres colonnes sous forme de lignes distinctes.

## <a name="daily-average-cycle"></a>Cycle moyen quotidien

Comment l’activité varie-t-elle au quotidien moyen ?

Nombre d’événements par Modulo d’une journée, Binned (en heures. Ici, nous utilisons à la `floor` place de `bin` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="Capture d’écran montrant un nombre graphique temporel par heure.":::

Actuellement, `render` n’étiquette pas les durées correctement, mais nous pourrions utiliser à la `| render columnchart` place :

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="Capture d’écran montrant un graphique en colonnes nombre par heure.":::

## <a name="compare-multiple-daily-series"></a>Comparer plusieurs séries quotidiennes

Comment l’activité varie-t-elle sur l’heure de la journée dans différents États ?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="Capture d’écran d’un graphique temporel par heure et état.":::

Diviser par `1h` pour transformer l’axe x en un nombre d’heures au lieu d’une durée :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="Capture d’écran montrant un histogramme par heure et par État.":::

## <a name="join-data-types"></a>Joindre des types de données

Comment trouver deux types d’événements spécifiques et dans quel état chacun s’est-il produit ?

Vous pouvez tirer les événements Storm avec le premier `EventType` et le deuxième `EventType` , puis joindre les deux jeux sur `State` :

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

:::image type="content" source="images/tutorial/join-events-lightning-avalanche.png" alt-text="Capture d’écran qui montre comment joindre la foudre et l’avalanche des événements.":::

## <a name="user-session-example-of-join"></a>Exemple de session utilisateur de *jointure*

Cette section n’utilise pas le `StormEvents` tableau.

Supposons que vous ayez des données qui incluent des événements qui marquent le début et la fin de chaque session utilisateur avec un ID unique pour chaque session. 

Comment déterminer la durée de chaque session utilisateur ?

Vous pouvez utiliser `extend` pour fournir un alias pour les deux horodatages, puis calculer la durée de la session :

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="Capture d’écran d’une table de résultats pour une session utilisateur Extend.":::

Il est recommandé d’utiliser `project` pour sélectionner uniquement les colonnes dont vous avez besoin avant d’effectuer la jointure. Dans les mêmes clauses, renommez la `timestamp` colonne.

## <a name="plot-a-distribution"></a>Tracer une distribution

En revenant au `StormEvents` tableau, combien de tempêtes sont-elles de différentes longueurs ?

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

Vous pouvez utiliser les `| render columnchart` éléments suivants :

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="Capture d’écran d’un histogramme pour le nombre d’événements graphique temporel par durée.":::

## <a name="percentiles"></a>Centiles

Quelles sont les plages de durées que nous trouvons dans différents pourcentages de Storm ?

Pour obtenir ces informations, utilisez la requête précédente, mais remplacez `render` par :

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Dans ce cas, nous n’avons pas utilisé de `by` clause, donc la sortie est une ligne unique :

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" lightbox="images/tutorial/summarize-percentiles-duration.png" alt-text="Capture d’écran d’un tableau de résultats pour résumer les centiles par durée.":::

Nous pouvons voir dans la sortie que :

* 5% des tempêtes ont une durée inférieure à 5 minutes.
* 50% des tempêtes ont été effectuées depuis moins d’une heure et 25 minutes.
* 5% des tempêtes ont été apprises au moins deux heures et 50 minutes.

Pour obtenir une répartition distincte pour chaque État, utilisez la `state` colonne séparément avec les deux `summarize` opérateurs :

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="Tableau résumer la durée des centiles par État.":::

## <a name="assign-a-result-to-a-variable-let"></a>Assigner un résultat à une variable : *Let*

Utilisez [Let](./letstatement.md) pour séparer les parties de l’expression de requête dans l' `join` exemple précédent. Les résultats sont identiques :

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

Dans la requête suivante, la `Logs` table doit figurer dans votre base de données par défaut :

```kusto
Logs | where ...
```

Pour accéder à une table dans une base de données différente, utilisez la syntaxe suivante :

```kusto
database("db").Table
```

Par exemple, si vous avez des bases de données nommées `Diagnostics` et que `Telemetry` vous souhaitez mettre en corrélation certaines données dans les deux tables, vous pouvez utiliser la requête suivante (en supposant que `Diagnostics` est votre base de données par défaut) :

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

Utilisez cette requête si votre base de données par défaut est `Telemetry` :

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Les deux requêtes précédentes supposent que les deux bases de données se trouvent dans le cluster auquel vous êtes actuellement connecté. Si la `Telemetry` base de données se trouvait dans un cluster nommé *TelemetryCluster.Kusto.Windows.net*, pour y accéder, utilisez la requête suivante :

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> Lorsque le cluster est spécifié, la base de données est obligatoire.

Pour plus d’informations sur la combinaison de données provenant de plusieurs bases de données dans une requête, consultez [requêtes de bases de données croisées](cross-cluster-or-database-queries.md).

## <a name="next-steps"></a>Étapes suivantes

- Affichez des exemples de code pour le [langage de requête Kusto](samples.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

La meilleure façon de se familiariser avec le langage de requête Kusto consiste à examiner certaines requêtes de base pour obtenir une « sensation » du langage. Ces requêtes sont similaires aux requêtes utilisées dans le didacticiel Azure Explorateur de données, mais elles utilisent à la place des données de tables communes dans un espace de travail Azure Log Analytics. 

Exécutez ces requêtes à l’aide de Log Analytics dans le Portail Azure. Log Analytics est un outil que vous pouvez utiliser pour écrire des requêtes de journal. Utilisez les données de journal dans Azure Monitor, puis évaluez les résultats de requête de journal. Si vous n’êtes pas familiarisé avec Log Analytics, suivez le [didacticiel log Analytics](/azure/azure-monitor/log-query/log-analytics-tutorial).

Toutes les requêtes de ce didacticiel utilisent l' [environnement de démonstration log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade). Vous pouvez utiliser votre propre environnement, mais vous ne disposez peut-être pas de certaines des tables qui sont utilisées ici. Étant donné que les données de l’environnement de démonstration ne sont pas statiques, les résultats de vos requêtes peuvent varier légèrement de ceux indiqués ici.


## <a name="count-rows"></a>Compter les lignes

La table [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) contient des données de performances collectées par des Insights, telles que des Azure Monitor pour machines virtuelles et des Azure Monitor pour les conteneurs. Pour déterminer la taille de la table, nous allons diriger son contenu dans un opérateur qui compte simplement les lignes.

Une requête est une source de données (généralement un nom de table), éventuellement suivie d’une ou plusieurs paires de caractères de barre verticale et d’un opérateur tabulaire. Dans ce cas, tous les enregistrements de la `InsightsMetrics` table sont renvoyés, puis envoyés à l' [opérateur Count](./countoperator.md). L' `count` opérateur affiche les résultats, car l’opérateur est la dernière commande de la requête.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

Voici le format :

|Count|
|-----|
|1 263 191|
    

## <a name="filter-by-boolean-expression-where"></a>Filtrer par expression booléenne : *Where*

La table [AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) contient des entrées du journal d’activité Azure, qui fournissent des informations sur tous les événements au niveau de l’abonnement ou du groupe d’administration qui se sont produits dans Azure. Nous allons voir uniquement les `Critical` entrées pendant une semaine spécifique.

L’opérateur [Where](/azure/data-explorer/kusto/query/whereoperator) est courant dans le langage de requête Kusto. `where` filtre une table sur des lignes qui correspondent à des critères spécifiques. L’exemple suivant utilise plusieurs commandes. Tout d’abord, la requête récupère tous les enregistrements de la table. Ensuite, il filtre les données uniquement pour les enregistrements qui se trouvent dans l’intervalle de temps. Enfin, il filtre les résultats uniquement pour les enregistrements qui ont un `Critical` niveau.

> [!NOTE]
> En plus de spécifier un filtre dans votre requête à l’aide de la `TimeGenerated` colonne, vous pouvez spécifier l’intervalle de temps dans log Analytics. Pour plus d’informations, consultez [journaliser l’étendue des requêtes et l’intervalle de temps dans Azure Monitor log Analytics](/azure/azure-monitor/log-query/scope).

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

:::image type="content" source="images/tutorial/azure-monitor-where-results.png" lightbox="images/tutorial/azure-monitor-where-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple d’opérateur Where.":::

## <a name="select-a-subset-of-columns-project"></a>Sélectionner un sous-ensemble de colonnes : *projet*

Utilisez [Project](./projectoperator.md) pour inclure uniquement les colonnes souhaitées. En s’appuyant sur l’exemple précédent, nous limitons la sortie à certaines colonnes :

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

:::image type="content" source="images/tutorial/azure-monitor-project-results.png" lightbox="images/tutorial/azure-monitor-project-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple d’opérateur de projet.":::

## <a name="show-n-rows-take"></a>Afficher *n* lignes : *Take*

[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) contient des données d’analyse pour les réseaux virtuels Azure. Utilisons l’opérateur [Take](./takeoperator.md) pour examiner cinq lignes d’exemple aléatoires dans cette table. Le [Take](./takeoperator.md) affiche un certain nombre de lignes d’une table sans ordre particulier :

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-take-results.png" lightbox="images/tutorial/azure-monitor-take-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple d’opérateur Take.":::

## <a name="order-results-sort-top"></a>Résultats de la commande : *Trier*, *haut*

Au lieu d’enregistrements aléatoires, nous pouvons retourner les cinq derniers enregistrements en commençant par le premier tri par heure :

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

Vous pouvez vous procurer ce comportement exact en utilisant plutôt l’opérateur [Top](./topoperator.md) : 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

:::image type="content" source="images/tutorial/azure-monitor-top-results.png" lightbox="images/tutorial/azure-monitor-top-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple d’opérateur Top.":::

## <a name="compute-derived-columns-extend"></a>Colonnes dérivées de calcul : *étendre*

L’opérateur [Extend](./projectoperator.md) est similaire à [Project](./projectoperator.md), mais il s’ajoute à l’ensemble de colonnes au lieu de les remplacer. Vous pouvez utiliser les deux opérateurs pour créer une colonne basée sur un calcul sur chaque ligne.

La table [perf](/azure/azure-monitor/reference/tables/perf) contient des données de performances collectées à partir d’ordinateurs virtuels qui exécutent l’agent log Analytics. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

:::image type="content" source="images/tutorial/azure-monitor-extend-results.png" lightbox="images/tutorial/azure-monitor-extend-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple d’opérateur Extend.":::

## <a name="aggregate-groups-of-rows-summarize"></a>Agréger des groupes de lignes : *synthétiser*

L’opérateur de [synthèse](./summarizeoperator.md) groupe les lignes qui ont les mêmes valeurs dans la `by` clause. Ensuite, il utilise une fonction d’agrégation comme `count` pour combiner chaque groupe dans une ligne unique. Une plage de [fonctions d’agrégation](./summarizeoperator.md#list-of-aggregation-functions) est disponible. Vous pouvez utiliser plusieurs fonctions d’agrégation dans un `summarize` opérateur pour produire plusieurs colonnes calculées. 

La table [SecurityEvent](/azure/azure-monitor/reference/tables/securityevent) contient des événements de sécurité tels que les ouvertures de session et les processus démarrés sur les ordinateurs analysés. Vous pouvez compter le nombre d’événements de chaque niveau qui se sont produits sur chaque ordinateur. Dans cet exemple, une ligne est produite pour chaque combinaison d’ordinateur et de niveau. Une colonne contient le nombre d’événements.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-count-results.png" lightbox="images/tutorial/azure-monitor-summarize-count-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple d’opérateur de cumul de nombre.":::

## <a name="summarize-by-scalar-values"></a>Résumer en fonction de valeurs scalaires

Vous pouvez agréger par valeurs scalaires comme des nombres et des valeurs d’heure, mais vous devez utiliser la fonction [bin ()](./binfunction.md) pour regrouper des lignes dans des ensembles de données distincts. Par exemple, si vous regroupez par `TimeGenerated` , vous obtenez une ligne pour presque chaque valeur de temps. Utilisez `bin()` pour consolider ces valeurs en heures ou en jours.

La table [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) contient des données de performances collectées par des Insights, telles que des Azure Monitor pour machines virtuelles et des Azure Monitor pour les conteneurs. La requête suivante affiche l’utilisation moyenne horaire du processeur pour plusieurs ordinateurs :

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```

:::image type="content" source="images/tutorial/azure-monitor-summarize-avg-results.png" lightbox="images/tutorial/azure-monitor-summarize-avg-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple AVG Operator.":::

## <a name="display-a-chart-or-table-render"></a>Afficher un graphique ou une table : *rendu*

L’opérateur [Render](./renderoperator.md?pivots=azuremonitor) spécifie le mode de rendu de la sortie de la requête. Log Analytics restitue la sortie sous forme de table par défaut. Vous pouvez sélectionner différents types de graphiques après l’exécution de la requête. L' `render` opérateur est utile pour inclure des requêtes dans lesquelles un type de graphique spécifique est généralement préféré.

L’exemple suivant montre l’utilisation moyenne horaire du processeur pour un ordinateur unique. Il restitue la sortie en tant que graphique temporel.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-results.png" lightbox="images/tutorial/azure-monitor-render-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple d’opérateur Render.":::

## <a name="work-with-multiple-series"></a>Travailler avec plusieurs séries

Si vous utilisez plusieurs valeurs dans une `summarize by` clause, le graphique affiche une série distincte pour chaque ensemble de valeurs :

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

:::image type="content" source="images/tutorial/azure-monitor-render-multiple-results.png" lightbox="images/tutorial/azure-monitor-render-multiple-results.png" alt-text="Capture d’écran qui montre les résultats de l’opérateur Render avec un exemple de série multiple.":::

## <a name="join-data-from-two-tables"></a>Joindre des données de deux tables

Que se passe-t-il si vous avez besoin de récupérer des données de deux tables dans une seule requête ? Vous pouvez utiliser l’opérateur de [jointure](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) pour combiner des lignes de plusieurs tables dans un seul jeu de résultats. Chaque table doit avoir une colonne qui a une valeur correspondante afin que la jointure comprenne les lignes à faire correspondre.

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer) est une table que Azure Monitor utilise pour les machines virtuelles pour stocker des détails sur les machines virtuelles qu’il surveille. [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) contient des données de performances collectées à partir de ces machines virtuelles. Une valeur collectée dans *InsightsMetrics* est une mémoire disponible, mais pas le pourcentage de mémoire disponible. Pour calculer le pourcentage, nous avons besoin de la mémoire physique pour chaque machine virtuelle. Cette valeur est dans `VMComputer` .

L’exemple de requête suivant utilise une jointure pour effectuer ce calcul. L’opérateur [distinct](/azure/data-explorer/kusto/query/distinctoperator) est utilisé avec `VMComputer` , car les détails sont régulièrement collectés à partir de chaque ordinateur. Par conséquent, plusieurs lignes sont créées pour chaque ordinateur de la table. Les deux tables sont jointes à l’aide de la `Computer` colonne. Une ligne est créée dans le jeu de résultats qui contient des colonnes des deux tables pour chaque ligne dans `InsightsMetrics` , avec une valeur de `Computer` qui correspond à la même valeur dans la `Computer` colonne de `VMComputer` .

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

:::image type="content" source="images/tutorial/azure-monitor-join-results.png" lightbox="images/tutorial/azure-monitor-join-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple d’opérateur de jointure.":::

## <a name="assign-a-result-to-a-variable-let"></a>Assigner un résultat à une variable : *Let*

Utilisez [Let](./letstatement.md) pour faciliter la lecture et la gestion des requêtes. Vous pouvez utiliser cet opérateur pour assigner les résultats d’une requête à une variable que vous pouvez utiliser ultérieurement. À l’aide de l' `let` instruction, la requête de l’exemple précédent peut être réécrite comme suit :

 
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

:::image type="content" source="images/tutorial/azure-monitor-let-results.png" lightbox="images/tutorial/azure-monitor-let-results.png" alt-text="Capture d’écran qui montre les résultats de l’exemple d’opérateur Let.":::

## <a name="next-steps"></a>Étapes suivantes

- Affichez des exemples de code pour le [langage de requête Kusto](samples.md?pivots=azuremonitor).


::: zone-end
