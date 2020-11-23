---
title: Didacticiel-Azure Explorateur de données
description: Cet article décrit le didacticiel dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8a3c0b058b2c1cf5023ce0069a7dd938fce5caec
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324735"
---
# <a name="tutorial"></a>Didacticiel

::: zone pivot="azuredataexplorer"

La meilleure façon de se familiariser avec le langage de requête Kusto consiste à examiner des requêtes simples pour obtenir le « sentiment » du langage à l’aide d’une [base de données avec des exemples de données](https://help.kusto.windows.net/Samples). Les requêtes illustrées dans cet article doivent s’exécuter sur cette base de données. La `StormEvents` table de cet exemple de base de données fournit des informations sur les tempêtes qui se sont produites aux États-Unis.

## <a name="count-rows"></a>Compter les lignes

Notre exemple de base de données a une table appelée `StormEvents` .
Pour en savoir plus sur la taille, nous allons diriger son contenu dans un opérateur qui compte simplement les lignes :

* *Syntaxe :* Une requête est une source de données (généralement un nom de table), éventuellement suivie d’une ou plusieurs paires de caractères de barre verticale et d’un opérateur tabulaire.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents | count
```

Voici le résultat :

|Count|
|-----|
|59066|
    
[opérateur de nombre](./countoperator.md).

## <a name="project-select-a-subset-of-columns"></a>projet : sélectionnez un sous-ensemble de colonnes

Utilisez [Project](./projectoperator.md) pour choisir uniquement les colonnes de votre choix. Consultez l’exemple ci-dessous qui utilise à la fois le [projet](./projectoperator.md) et l’opérateur [Take](./takeoperator.md) .

## <a name="where-filtering-by-a-boolean-expression"></a>WHERE : filtrage par une expression booléenne

Nous allons voir uniquement les `flood` dans le `California` cadre du 1er février-2007 :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|State (État)|Type d’événement|EpisodeNarrative|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|France|Crue|Un système frontal qui évolue à travers le sud de la Joaquin de la vallée A introduit de courtes périodes de pluie lourde dans le comté de crénage occidental au cours des premières heures du 19. Une saturation mineure a été signalée sur l’autoroute d’État 166 près de Taft.|

## <a name="take-show-me-n-rows"></a>Take : afficher n lignes

Examinons quelques données : quel est le contenu d’un échantillon de 5 lignes ?

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|Type d’événement|State (État)|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Pluie lourde|Floride|Jusqu’à 9 pouces de pluie chutaient sur une période de 24 heures sur des parties du comté Volusia côtier.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Tornade|Floride|Tornade évoqué dans la ville de Eustis, à la fin du Nord de l’ouest du lac. La tornade s’est rapidement intensifiée sur la force EF1 à mesure qu’elle a été déplacée Nord-Nord-Ouest à Eustis. La piste se trouvait juste à moins de deux kilomètres et avait une largeur maximale de 300 mètres.  La tornade a détruit 7 maisons. Vingt sept maisons ont reçu des dommages majeurs et 81 maisons signalaient des dommages mineurs. Il n’y avait pas de blessures graves et les dommages liés aux propriétés ont été définis à $6,2 millions.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Waterspout|SUD DE L’ATLANTIQUE|Un Waterspout formé dans le sud-est de l’Atlantique de Melbourne Beach et placé brièvement vers la terre.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Vent d’orage|MISSISSIPPI|De nombreux arbres de grande taille ont été décomposés de lignes d’alimentation. Les dommages se sont produits dans le comté de l’est Adams.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Vent d’orage|Géorgie|La distribution de la région a rapporté que plusieurs arbres ont été relevés le long de la route Quincey-Loop à l’état près de la route 206. Le coût de la suppression de l’arborescence a été estimé.|

Mais [Take](./takeoperator.md) affiche les lignes de la table dans un ordre particulier, donc nous allons les trier.
* [Limit](./takeoperator.md) est un alias pour [Take](./takeoperator.md) et a le même effet.

## <a name="sort-and-top"></a>Trier et haut

* *Syntaxe :* Certains opérateurs ont des paramètres introduits par des mots clés tels que `by` .
* `desc` = ordre décroissant, `asc` = ordre croissant.

Afficher les n premières lignes, classées par une colonne particulière :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|Type d’événement|State (État)|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête d’hiver|MICHIGAN|Cet événement lourd a continué au début des heures du matin le jour de la nouvelle année.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête d’hiver|MICHIGAN|Cet événement lourd a continué au début des heures du matin le jour de la nouvelle année.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête d’hiver|MICHIGAN|Cet événement lourd a continué au début des heures du matin le jour de la nouvelle année.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Vent élevé|France|Les vents du Nord vers le nord-est à environ 58 km/h sont signalés dans les montagnes du comté Ventura.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Vent élevé|France|Le capteur RAWS de ressorts à chaud a signalé un rafale des vents à 58 km/h.|

Le même résultat peut être obtenu à l’aide de l’opérateur [sort](./sortoperator.md) et [Take](./takeoperator.md)

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndTime, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>Extend : calcul des colonnes dérivées

Créez une nouvelle colonne en calculant une valeur dans chaque ligne :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|Duration|Type d’événement|State (État)|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Pluie lourde|Floride|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Tornade|Floride|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Waterspout|SUD DE L’ATLANTIQUE|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Vent d’orage|MISSISSIPPI|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Vent d’orage|Géorgie|

Il est possible de réutiliser le nom de colonne et d’assigner le résultat de calcul à la même colonne.
Exemple :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

Les [expressions scalaires](./scalar-data-types/index.md) peuvent inclure tous les opérateurs habituels ( `+` , `-` ,, `*` `/` , `%` ), et il existe une gamme de fonctions utiles.

## <a name="summarize-aggregate-groups-of-rows"></a>Résumé : groupes d’agrégats de lignes

Compter le nombre d’événements provenant de chaque pays :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count = count() by State
```

[résume](./summarizeoperator.md) les groupes ensemble de lignes qui ont les mêmes valeurs dans la `by` clause, puis utilise la fonction d’agrégation (telle que `count` ) pour combiner chaque groupe dans une ligne unique. Ainsi, dans ce cas, il y a une ligne pour chaque État et une colonne pour le nombre de lignes dans cet État.

Il existe une plage de [fonctions d’agrégation](./summarizeoperator.md#list-of-aggregation-functions), et vous pouvez en utiliser plusieurs dans un opérateur de synthèse pour produire plusieurs colonnes calculées. Par exemple, nous pourrions obtenir le nombre de tempêtes dans chaque État et également une somme du type de tempêtes unique par État,  
Ensuite, nous pourrions utiliser [Top](./topoperator.md) pour connaître les États les plus affectés à la Storm :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|State (État)|StormCount|TypeOfStorms|
|---|---|---|
|TEXAS|4701|27|
|KANSAS|3166|21|
|IOWA|2337|19|
|ILLINOIS|2022|23|
|MISSOURI|2016|20|

Le résultat d’un résumé contient :

* chaque colonne nommée dans `by`;
* une colonne pour chaque expression calculée ;
* une ligne pour chaque combinaison de valeurs `by` .

## <a name="summarize-by-scalar-values"></a>Résumer en fonction de valeurs scalaires

Vous pouvez utiliser des valeurs scalaires (numériques, horaires ou d’intervalle) dans la `by` clause, mais vous souhaiterez placer les valeurs dans des emplacements.  
La fonction [bin ()](./binfunction.md) est utile dans les cas suivants :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

Cela réduit tous les horodateurs aux intervalles de 1 jour :

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

## <a name="render-display-a-chart-or-table"></a>Render : afficher un graphique ou un tableau

Projetez deux colonnes et utilisez-les comme axe des x et y d’un graphique :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tutorial/event-counts-state.png" alt-text="Histogramme du nombre d’événements Storm par État":::

Bien que nous ayons supprimé l' `mid` opération de projet, nous en avons encore besoin si nous voulons que le graphique affiche les pays dans cet ordre.

Strictement parlant, « Render » est une fonctionnalité du client au lieu d’une partie du langage de requête. Toutefois, il est intégré dans le langage et est très utile pour la vision de vos résultats.


## <a name="timecharts"></a>Graphiques temporels

En revenons aux emplacements numériques, nous affichons une série chronologique :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tutorial/time-series-start-bin.png" alt-text="Événements de graphique en courbes Binned (par heure":::

## <a name="multiple-series"></a>Séries multiples

Utilisez plusieurs valeurs dans une clause `summarize by` afin de créer une ligne distincte pour chaque combinaison de valeurs :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tutorial/table-count-source.png" alt-text="Nombre de tables par source":::

Ajoutez simplement le terme de rendu à l’expression ci-dessus : `| render timechart` .

:::image type="content" source="images/tutorial/line-count-source.png" alt-text="Nombre de graphiques en courbes par source":::

Notez que `render timechart` utilise la première colonne comme axe des abscisses, puis affiche les autres colonnes sous forme de lignes distinctes.

## <a name="daily-average-cycle"></a>Cycle moyen quotidien

Comment l’activité varie-t-elle au quotidien moyen ?

Nombre d’événements par Modulo d’une journée, Binned (en heures. Notez que nous utilisons à la `floor` place de bin :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tutorial/time-count-hour.png" alt-text="Graphique chronologique nombre par heure":::

Actuellement, `render` n’étiquette pas les durées correctement, mais nous pourrions utiliser à la `| render columnchart` place :

:::image type="content" source="images/tutorial/column-count-hour.png" alt-text="Nombre de graphiques en colonnes par heure":::

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

:::image type="content" source="images/tutorial/time-hour-state.png" alt-text="Graphique temporel par heure et état":::

Diviser par `1h` pour transformer l’axe x en nombre d’heures au lieu d’une durée :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tutorial/column-hour-state.png" alt-text="Histogramme par heure et état":::

## <a name="join"></a>Join

Comment trouver deux EventTypes donnés dans quel état ces deux événements se sont-ils produits ?

Vous pouvez extraire des événements Storm avec le premier EventType et le second EventType, puis joindre les deux jeux sur l’État.

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

:::image type="content" source="images/tutorial/join-events-la.png" alt-text="Événements de jointure Lightning et avalanche":::

## <a name="user-session-example-of-join"></a>Exemple de session utilisateur de jointure

Cette section n’utilise pas la `StormEvents` table.

Supposons que vous ayez des données qui incluent des événements marquant le début et la fin de chaque session utilisateur, avec un ID unique pour chaque session. 

Quelle est la durée de chaque session utilisateur ?

En utilisant `extend` pour fournir un alias pour les deux horodatages, vous pouvez calculer la durée de la session.

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

:::image type="content" source="images/tutorial/user-session-extend.png" alt-text="Extension de session utilisateur":::

Avant d’effectuer la jointure, nous pouvons utiliser `project` pour sélectionner uniquement les colonnes dont nous avons besoin.
Dans les mêmes clauses, nous renommons la colonne timestamp.

## <a name="plot-a-distribution"></a>Tracer une distribution

Combien de tempêtes présentent des longueurs différentes ?

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

:::image type="content" source="images/tutorial/event-count-duration.png" alt-text="Nombre d’événements graphique temporel par durée":::

Ou utilisez `| render columnchart` :

:::image type="content" source="images/tutorial/column-event-count-duration.png" alt-text="Nombre d’événements de graphique en colonnes graphique temporel par durée":::

## <a name="percentiles"></a>Centiles

Quelles sont les plages de durées qui couvrent différents pourcentages de Storm ?

Utilisez la requête ci-dessus, mais remplacez `render` par :

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Dans ce cas, nous n’avons fourni aucune `by` clause, donc le résultat est une ligne unique :

:::image type="content" source="images/tutorial/summarize-percentiles-duration.png" alt-text="Tableau de synthèse des centiles par durée":::

Nous pouvons voir que :

* 5% des tempêtes ont une durée inférieure à 5m ;
* 50% des tempêtes inférieurs à 1H 25m ;
* 5% des tempêtes durent au moins 2H 50m.

Pour obtenir une répartition distincte pour chaque État, nous devons simplement placer la colonne State séparément via les deux opérateurs de synthèse :

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

:::image type="content" source="images/tutorial/summarize-percentiles-state.png" alt-text="Table synthétiser la durée des centiles par État":::


## <a name="let-assign-a-result-to-a-variable"></a>Let: affecter un résultat à une variable

Utilisez [Let](./letstatement.md) pour séparer les parties de l’expression de requête dans l’exemple « Join » ci-dessus. Les résultats sont identiques :

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
> Dans le client Kusto Explorer, ne placez pas de lignes vides entre les parties de ce. Exécutez-la en totalité.

## <a name="combining-data-from-several-databases-in-a-query"></a>Combinaison de données de plusieurs bases de données dans une requête

Consultez [requêtes de bases de données croisées](./cross-cluster-or-database-queries.md) pour une discussion détaillée

Lorsque vous écrivez une requête du style :

```kusto
Logs | where ...
```

La table nommée logs doit se trouver dans votre base de données par défaut. Si vous souhaitez accéder à des tables à partir d’une autre base de données, utilisez la syntaxe suivante :

```kusto
database("db").Table
```

Par conséquent, si vous avez des bases de données nommées *Diagnostics* et *télémétrie* et que vous souhaitez mettre en corrélation certaines de leurs données, vous pouvez écrire (en supposant que *Diagnostics* est votre base de données par défaut).

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

ou si votre base de données par défaut est *télémétrie*

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Tous les éléments ci-dessus supposent que les deux bases de données se trouvent dans le cluster auquel vous êtes actuellement connecté. Supposons que la base de données de *télémétrie* appartenait à un autre cluster nommé *TelemetryCluster.Kusto.Windows.net* , puis à y accéder.

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> [!NOTE]
> Lorsque le cluster est spécifié, la base de données est obligatoire




::: zone-end

::: zone pivot="azuremonitor"

La meilleure façon de se familiariser avec le langage de requête Kusto consiste à examiner des requêtes simples pour obtenir le « sentiment » du langage. Ces requêtes sont similaires à celles utilisées dans le didacticiel Azure Explorateur de données, mais elles utilisent des données de tables communes dans un espace de travail Log Analytics. 

Exécutez ces requêtes à l’aide de Log Analytics, qui est un outil du Portail Azure pour écrire des requêtes de journal en utilisant des données de journal dans Azure Monitor et évaluer leurs résultats. Si vous n’êtes pas familiarisé avec Log Analytics, vous pouvez suivre un didacticiel dans [log Analytics didacticiel](/azure/azure-monitor/log-query/log-analytics-tutorial).

Toutes les requêtes ici utilisent l' [environnement de démonstration log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade). Vous pouvez utiliser votre propre environnement, mais vous ne disposez peut-être pas de certaines des tables utilisées ici. Dans la mesure où les données de l’environnement de démonstration ne sont pas statiques, les résultats de vos requêtes peuvent varier légèrement de ceux indiqués ici.


## <a name="count-rows"></a>Compter les lignes
[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) contient des données de performances collectées par des Insights, telles que Azure Monitor pour machines virtuelles et Azure Monitor pour les conteneurs. Pour en savoir plus sur la taille, nous allons diriger son contenu dans un opérateur qui compte simplement les lignes :

Une requête est une source de données (généralement un nom de table), éventuellement suivie d’une ou plusieurs paires de caractères de barre verticale et d’un opérateur tabulaire. Dans ce cas, tous les enregistrements de la table InsightsMetrics sont renvoyés, puis envoyés à l’opérateur [Count Operator](./countoperator.md) qui génère les résultats, car il s’agit de la dernière commande de la requête.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
InsightsMetrics | count
```

Voici le résultat :

|Count|
|-----|
|1 263 191|
    



## <a name="where-filtering-by-a-boolean-expression"></a>WHERE : filtrage par une expression booléenne
[AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) contient des entrées du journal d’activité Azure qui fournissent des informations sur les événements de niveau abonnement ou groupe d’administration qui se sont produits dans Azure. Nous allons voir uniquement les `Critical` entrées pendant une semaine spécifique.


L’opérateur [Where](/azure/data-explorer/kusto/query/whereoperator) est très courant dans KQL et filtre un tableau sur les lignes qui correspondent aux critères spécifiés. Cet exemple utilise plusieurs commandes. La requête extrait tout d’abord tous les enregistrements de la table, filtre ces données uniquement pour les enregistrements de l’intervalle de temps, puis filtre les résultats pour les enregistrements juste avec un `Critical` niveau.

> [!NOTE]
> En plus de spécifier un filtre dans votre requête à l’aide de la `TimeGenerated` colonne, vous pouvez spécifier l’intervalle de temps dans log Analytics. Pour plus d’informations, consultez [Étendue de requête de journal et intervalle de temps dans la fonctionnalité Log Analytics d’Azure Monitor](/azure/azure-monitor/log-query/scope).

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
```

[![Résultats de l’exemple de filtrage Where](images/tutorial/am-results-where.png)](images/tutorial/am-results-where.png#lightbox)


## <a name="project-select-a-subset-of-columns"></a>projet : sélectionnez un sous-ensemble de colonnes

Utilisez [Project](./projectoperator.md) pour choisir uniquement les colonnes de votre choix. En s’appuyant sur l’exemple précédent, nous limitons la sortie à certaines colonnes.

```kusto
AzureActivity
| where TimeGenerated > datetime(10-01-2020) and TimeGenerated < datetime(10-07-2020)
| where Level == 'Critical'
| project TimeGenerated, Level, OperationNameValue, ResourceGroup, _ResourceId
```

[![Exemple de résultats de projet](images/tutorial/am-results-project.png)](images/tutorial/am-results-project.png#lightbox)


## <a name="take-show-me-n-rows"></a>Take : afficher n lignes
[NetworkMonitoring](/azure/azure-monitor/reference/tables/networkmonitoring) contient des données d’analyse pour les réseaux virtuels Azure. Utilisons l’opérateur [Take pour jeter](./takeoperator.md) un coup d’œil sur 5 lignes d’exemple dans cette table. La commande [Take](./takeoperator.md) affiche un certain nombre de lignes d’une table dans un ordre particulier.

```kusto
NetworkMonitoring
| take 10
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![Résultats de l’exemple Take](images/tutorial/am-results-take.png)](images/tutorial/am-results-take.png#lightbox)

## <a name="sort-and-top"></a>Trier et haut
Au lieu d’enregistrements aléatoires, nous pouvons retourner les 5 derniers enregistrements en commençant par le premier tri par heure.

```kusto
NetworkMonitoring
| sort by TimeGenerated desc
| take 5
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

Vous pouvez vous procurer ce comportement exact en utilisant plutôt l’opérateur [Top](./topoperator.md) . 

```kusto
NetworkMonitoring
| top 5 by TimeGenerated desc
| project TimeGenerated, Computer, SourceNetwork, DestinationNetwork, HighLatency, LowLatency
```

[![Résultats de l’exemple Top](images/tutorial/am-results-top.png)](images/tutorial/am-results-top.png#lightbox)


## <a name="extend-compute-derived-columns"></a>Extend : calcul des colonnes dérivées
L’opérateur [Extend](./projectoperator.md) est semblable au [projet](./projectoperator.md) , sauf qu’il est ajouté à l’ensemble de colonnes au lieu de les remplacer. Vous pouvez également utiliser les deux opérateurs pour créer une colonne basée sur un calcul sur chaque ligne.

La table [perf](/azure/azure-monitor/reference/tables/perf) contient des données de performances collectées à partir d’ordinateurs virtuels exécutant l’agent log Analytics. 

```kusto
Perf
| where ObjectName == "LogicalDisk" and CounterName == "Free Megabytes"
| project TimeGenerated, Computer, FreeMegabytes = CounterValue
| extend FreeGigabytes = FreeMegabytes / 1000
```

[![Résultats de l’exemple Extend](images/tutorial/am-results-extend.png)](images/tutorial/am-results-extend.png#lightbox)


## <a name="summarize-aggregate-groups-of-rows"></a>Résumé : groupes d’agrégats de lignes
L’opérateur de [synthèse](./summarizeoperator.md) rassemble des lignes qui ont les mêmes valeurs dans la `by` clause, puis utilise une fonction d’agrégation telle que `count` pour combiner chaque groupe en une seule ligne. Il existe une plage de [fonctions d’agrégation](./summarizeoperator.md#list-of-aggregation-functions), et vous pouvez en utiliser plusieurs dans un opérateur de synthèse pour produire plusieurs colonnes calculées. 

Le [SecurityEvent](/azure/azure-monitor/reference/tables/securityevent) contient les événements de sécurité tels que les ouvertures de session et les processus qui commencent sur les ordinateurs analysés. Nous pouvons compter le nombre d’événements de chaque niveau qui se sont produits sur chaque ordinateur. Dans cet exemple, il existe une ligne pour chaque combinaison d’ordinateurs et de niveaux et une colonne pour le nombre d’événements.

```kusto
SecurityEvent
| summarize count() by Computer, Level
```

[![Résultats de l’exemple de résumé de nombre](images/tutorial/am-results-summarize-count.png)](images/tutorial/am-results-summarize-count.png#lightbox)


## <a name="summarize-by-scalar-values"></a>Résumer en fonction de valeurs scalaires
Vous pouvez agréger par valeurs scalaires, telles que des nombres et des valeurs d’heure, mais vous devez utiliser la fonction [bin ()](./binfunction.md) pour regrouper des lignes dans des jeux de données distincts. Par exemple, si vous regroupez par `TimeGenerated` , vous obtenez une ligne pour presque chaque valeur de temps. `bin()` pour consolider ces valeurs en heures ou en jours.

[InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) contient des données de performances collectées par des Insights, telles que Azure Monitor pour machines virtuelles et Azure Monitor pour les conteneurs. La requête suivante affiche l’utilisation moyenne horaire du processeur pour plusieurs ordinateurs.

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
```


[![Résultats de la synthèse de l’exemple AVG](images/tutorial/am-results-summarize-avg.png)](images/tutorial/am-results-summarize-avg.png#lightbox)



## <a name="render-display-a-chart-or-table"></a>Render : afficher un graphique ou un tableau
L’opérateur [Render](./renderoperator.md?pivots=azuremonitor) spécifie le mode de rendu de la sortie de la requête. Log Analytics affichent la sortie sous forme de table par défaut, et vous pouvez sélectionner différents types de graphiques après l’exécution de la requête. L' `render` opérateur peut être utilisé dans les requêtes où un type de graphique particulier est généralement préféré.

L’exemple suivant montre l’utilisation moyenne horaire du processeur pour un ordinateur unique et restitue la sortie sous la forme d’un graphique temporel.

```kusto
InsightsMetrics
| where Computer == "DC00.NA.contosohotels.com"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```

[![Résultats de l’exemple Render](images/tutorial/am-results-render.png)](images/tutorial/am-results-render.png#lightbox)



## <a name="multiple-series"></a>Séries multiples
S’il existe plusieurs valeurs dans une `summarize by` clause, le graphique affiche une série distincte pour chaque ensemble de valeurs :

```kusto
InsightsMetrics
| where Computer startswith "DC"
| where Namespace  == "Processor" and Name == "UtilizationPercentage"
| summarize avg(Val) by Computer, bin(TimeGenerated, 1h)
| render timechart
```


[![Exemple de résultats du rendu avec plusieurs séries](images/tutorial/am-results-render-multiple.png)](images/tutorial/am-results-render-multiple.png#lightbox)

## <a name="join-data-from-two-tables"></a>Joindre des données de deux tables
Que se passe-t-il si vous avez besoin de récupérer des données de deux tables dans une seule requête ? L’opérateur de [jointure](/azure/data-explorer/kusto/query/joinoperator?pivots=azuremonitor) vous permet de combiner des lignes de plusieurs tables en un seul jeu de résultats. Chaque table doit avoir une colonne avec une valeur correspondante afin que la jointure comprenne les lignes à faire correspondre.

[VMComputer](/azure/azure-monitor/reference/tables/vmcomputer) est une table utilisée par Azure Monitor pour machines virtuelles pour stocker des détails sur les machines virtuelles qu’il surveille. [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) contient les données de performances collectées à partir de ces machines virtuelles. Une valeur collectée dans *InsightsMetrics* est la mémoire disponible, mais pas le pourcentage de mémoire disponible. Pour calculer le pourcentage, nous avons besoin de la mémoire physique pour chaque machine virtuelle qui se trouve dans *VMComputer*.

L’exemple de requête suivant utilise une jointure pour effectuer ce calcul. Le [distinct](/azure/data-explorer/kusto/query/joinoperator) est utilisé avec *VMComputer* , car les détails sont régulièrement collectés à partir de chaque ordinateur créant plusieurs lignes pour chaque ordinateur de cette table. Les deux tables sont jointes à l’aide de la colonne *ordinateur* . Cela signifie qu’une ligne est créée dans le jeu de résultats qui contient des colonnes des deux tables pour chaque ligne dans *InsightsMetrics* avec une valeur dans *ordinateur* qui correspond à la même valeur dans la colonne *ordinateur* dans *VMComputer*.

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

[![Exemple des résultats de la jointure](images/tutorial/am-results-join.png)](images/tutorial/am-results-join.png#lightbox)


## <a name="let-assign-a-result-to-a-variable"></a>Let: affecter un résultat à une variable
Utilisez [Let](./letstatement.md) pour faciliter la lecture et la gestion des requêtes. Cet opérateur vous permet d’assigner les résultats d’une requête à une variable que vous pouvez utiliser ultérieurement. La même requête dans l’exemple précédent peut être réécrite comme suit.

 
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

[![Résultats de l’exemple Let](images/tutorial/am-results-let.png)](images/tutorial/am-results-let.png#lightbox)

## <a name="next-steps"></a>Étapes suivantes

- [Affichez des exemples de code pour le langage de requête Kusto](samples.md?pivots=azuremonitor).


::: zone-end
