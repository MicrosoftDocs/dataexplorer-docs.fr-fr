---
title: Tutorial - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Tutorial dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/23/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4720d44396fbb30350a4113fa798d7d179d7ae85
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765769"
---
# <a name="tutorial"></a>Didacticiel

::: zone pivot="azuredataexplorer"

La meilleure façon d’en apprendre davantage sur le langage de requête Kusto est d’examiner quelques requêtes simples pour obtenir le «sentir» pour la langue à l’aide [d’une base de données avec quelques données d’échantillons](https://help.kusto.windows.net/Samples). Les requêtes démontrées dans cet article devraient s’exécuter sur cette base de données. Le `StormEvents` tableau de cette base de données d’échantillons fournit des renseignements sur les tempêtes qui se sont produites aux États-Unis.

<!--
  TODO: Provide link to reference data we used originally in StormEvents
-->

<!--
  TODO: A few samples below reference non-existent tables, such as Events and Logs.
        We need to add these tables.
-->

## <a name="count-rows"></a>Compter les lignes

Notre base de données `StormEvents`d’exemple a un tableau appelé .
Pour savoir à quel point il est grand, nous allons canalter son contenu dans un opérateur qui compte simplement les lignes:

* *Syntaxe:* Une requête est une source de données (généralement un nom de table), suivie en option par une ou plusieurs paires de caractère de tuyau et un opérateur tabulaire.

```kusto
StormEvents | count
```

Voici le résultat :

|Count|
|-----|
|59066|
    
[compte opérateur](./countoperator.md).

## <a name="project-select-a-subset-of-columns"></a>projet : sélectionnez un sous-ensemble de colonnes

Utilisez [le projet](./projectoperator.md) pour choisir uniquement les colonnes que vous voulez. Voir l’exemple ci-dessous qui utilise à la fois [le projet](./projectoperator.md) et [l’opérateur de prise.](./takeoperator.md)

## <a name="where-filtering-by-a-boolean-expression"></a>où: filtrage par une expression Boolean

Voyons seulement le `flood`s `California` in pendant février-2007:

```kusto
StormEvents
| where StartTime > datetime(2007-02-01) and StartTime < datetime(2007-03-01)
| where EventType == 'Flood' and State == 'CALIFORNIA'
| project StartTime, EndTime , State , EventType , EpisodeNarrative
```

|StartTime|EndTime|State|Type d’événement|ÉpisodeNarratif|
|---|---|---|---|---|
|2007-02-19 00:00:00.0000000|2007-02-19 08:00:00.0000000|Californie|Crue|Un système frontal se déplaçant à travers la vallée méridionale de San Joaquin a apporté de brèves périodes de fortes pluies à l’ouest du comté de Kern dans les premières heures du matin du 19. Des inondations mineures ont été signalées sur la route 166 près de Taft.|

## <a name="take-show-me-n-rows"></a>prendre: montrez-moi n lignes

Examinons quelques données : quel est le contenu d’un échantillon de 5 lignes ?

```kusto
StormEvents
| take 5
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|Type d’événement|State|EventNarrative|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|Pluie abondante|Floride|Jusqu’à 9 pouces de pluie sont tombés dans une période de 24 heures à travers certaines parties côtières du comté de Volusia.|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|Tornade|Floride|Une tornade s’est abattue dans la ville d’Eustis, à l’extrémité nord du lac Crooked Ouest. La tornade s’est rapidement intensifiée à ef1 alors qu’elle se déplaçait vers le nord-ouest à travers Eustis. La piste était d’un peu moins de deux milles de long et avait une largeur maximale de 300 verges.  La tornade a détruit 7 maisons. Vingt-sept maisons ont subi des dommages importants et 81 maisons ont signalé des dommages mineurs. Il n’y a pas eu de blessures graves et les dommages matériels ont été fixés à 6,2 millions de dollars.|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|Trombe d’eau|ATLANTIQUE SUD|Une trombe d’eau s’est formée dans l’Atlantique au sud-est de Melbourne Beach et s’est brièvement déplacée vers la rive.|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|Vent d’orage|Mississippi|De nombreux grands arbres ont été abattus avec certains vers le bas sur les lignes électriques. Des dommages se sont produits dans l’est du comté d’Adams.|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|Vent d’orage|Géorgie|La dépêche du comté a signalé plusieurs arbres ont été soufflés le long de Quincey Batten Loop près de State Road 206. Le coût de l’enlèvement des arbres a été estimé.|

Mais [prenez](./takeoperator.md) des rangées de spectacles de la table dans aucun ordre particulier, alors nous allons les trier.
* [limite](./takeoperator.md) est un alias pour [prendre](./takeoperator.md) et aura le même effet.

## <a name="sort-and-top"></a>trier et haut

* *Syntaxe:* Certains opérateurs ont des paramètres introduits par des mots clés tels que `by`.
* `desc` = ordre décroissant, `asc` = ordre croissant.

Afficher les n premières lignes, classées par une colonne particulière :

```kusto
StormEvents
| top 5 by StartTime desc
| project  StartTime, EndTime, EventType, State, EventNarrative  
```

|StartTime|EndTime|Type d’événement|State|EventNarrative|
|---|---|---|---|---|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête hivernale|MICHIGAN|Cet épisode de neige abondante s’est poursuivi jusqu’aux premières heures du matin le jour de l’An.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête hivernale|MICHIGAN|Cet épisode de neige abondante s’est poursuivi jusqu’aux premières heures du matin le jour de l’An.|
|2007-12-31 22:30:00.0000000|2007-12-31 23:59:00.0000000|Tempête hivernale|MICHIGAN|Cet épisode de neige abondante s’est poursuivi jusqu’aux premières heures du matin le jour de l’An.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Vent élevé|Californie|Des vents du nord au nord-est soufflant en rafales à environ 58 mi/h ont été signalés dans les montagnes du comté de Ventura.|
|2007-12-31 23:53:00.0000000|2007-12-31 23:53:00.0000000|Vent élevé|Californie|Le capteur RawS de Warm Springs a signalé des vents du nord soufflant en rafales à 58 mi/h.|

Il en va de même en utilisant [le tri,](./sortoperator.md) puis [en prenant](./takeoperator.md) l’opérateur

```kusto
StormEvents
| sort by StartTime desc
| take 5
| project  StartTime, EndLat, EventType, EventNarrative
```

## <a name="extend-compute-derived-columns"></a>étendre: calculer les colonnes dérivées

Créez une nouvelle colonne en calculant une valeur dans chaque rangée :

```kusto
StormEvents
| limit 5
| extend Duration = EndTime - StartTime 
| project StartTime, EndTime, Duration, EventType, State
```

|StartTime|EndTime|Duration|Type d’événement|State|
|---|---|---|---|---|
|2007-09-18 20:00:00.0000000|2007-09-19 18:00:00.0000000|22:00:00|Pluie abondante|Floride|
|2007-09-20 21:57:00.0000000|2007-09-20 22:05:00.0000000|00:08:00|Tornade|Floride|
|2007-09-29 08:11:00.0000000|2007-09-29 08:11:00.0000000|00:00:00|Trombe d’eau|ATLANTIQUE SUD|
|2007-12-20 07:50:00.0000000|2007-12-20 07:53:00.0000000|00:03:00|Vent d’orage|Mississippi|
|2007-12-30 16:00:00.0000000|2007-12-30 16:05:00.0000000|00:05:00|Vent d’orage|Géorgie|

Il est possible de réutiliser le nom de colonne et d’attribuer le résultat de calcul à la même colonne.
Par exemple :

```kusto
print x=1
| extend x = x + 1, y = x
| extend x = x + 1
```

|x|y|
|---|---|
|3|1|

[Les expressions scalaires](./scalar-data-types/index.md) peuvent inclure`+` `-`tous `*` `/`les `%`opérateurs habituels (, , , , ), et il ya une gamme de fonctions utiles.

## <a name="summarize-aggregate-groups-of-rows"></a>résumé : groupes agrégés de lignes

Comptez le nombre d’événements de chaque pays :

```kusto
StormEvents
| summarize event_count = count() by State
```

[résumer](./summarizeoperator.md) les groupes ensemble les lignes `by` qui ont les mêmes valeurs dans la `count`clause, puis utilise la fonction d’agrégation (comme ) pour combiner chaque groupe en une seule rangée. Donc, dans ce cas, il ya une rangée pour chaque état, et une colonne pour le nombre de lignes dans cet état.

Il ya une gamme de [fonctions d’agrégation](./summarizeoperator.md#list-of-aggregation-functions), et vous pouvez utiliser plusieurs d’entre eux dans un opérateur de résumé pour produire plusieurs colonnes calculées. Par exemple, nous pourrions obtenir le nombre de tempêtes dans chaque état et aussi une somme du type unique de tempêtes par État,  
alors nous pourrions utiliser [le haut](./topoperator.md) pour obtenir les états les plus touchés par la tempête:

```kusto
StormEvents 
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

|State|StormCount (StormCount)|TypeOfStorms|
|---|---|---|
|TEXAS|4701|27|
|Kansas|3166|21|
|IOWA|2337|19|
|Illinois|2022|23|
|Missouri|2016|20|

Le résultat d’un résumé contient :

* chaque colonne nommée dans `by`;
* une colonne pour chaque expression calculée;
* une ligne pour chaque combinaison de valeurs `by` .

## <a name="summarize-by-scalar-values"></a>Résumer en fonction de valeurs scalaires

Vous pouvez utiliser des valeurs scalar (numérique, `by` temporelle ou d’intervalle) dans la clause, mais vous voudrez mettre les valeurs dans les bacs.  
La fonction [bin()](./binfunction.md) est utile pour ceci :

```kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

Cela réduit tous les délais à des intervalles de 1 jour:

|StartTime|event_count|
|---|---|
|2007-02-14 00:00:00.0000000|180|
|2007-02-15 00:00:00.0000000|66|
|2007-02-16 00:00:00.0000000|164|
|2007-02-17 00:00:00.0000000|103|
|2007-02-18 00:00:00.0000000|22|
|2007-02-19 00:00:00.0000000|52|
|2007-02-20 00:00:00.0000000|60|

Le [bac()](./binfunction.md) est le même que le [plancher ()](./floorfunction.md) fonction dans de nombreuses langues. Il réduit simplement chaque valeur au multiple le plus proche du modulus que vous fournissez, de sorte [que](./summarizeoperator.md) résumer peut attribuer les lignes aux groupes.

## <a name="render-display-a-chart-or-table"></a>Rendu : afficher un graphique ou une table

Projetez deux colonnes et utilisez-les comme axe x et y d’un graphique :

```kusto
StormEvents 
| summarize event_count=count(), mid = avg(BeginLat) by State 
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

:::image type="content" source="images/tour/060.png" alt-text="060":::

Bien que `mid` nous ayons supprimé dans l’opération du projet, nous en avons encore besoin si nous voulons que le graphique affiche les pays dans cet ordre.

Strictement parlant, «rendu» est une caractéristique du client plutôt que de faire partie du langage de la requête. Pourtant, il est intégré dans la langue et est très utile pour imaginer vos résultats.


## <a name="timecharts"></a>Graphiques temporels

Pour en revenir aux bacs numériques, montrons une série de chronos :

```kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

:::image type="content" source="images/tour/080.png" alt-text="080":::

## <a name="multiple-series"></a>Séries multiples

Utilisez plusieurs valeurs dans une clause `summarize by` afin de créer une ligne distincte pour chaque combinaison de valeurs :

```kusto
StormEvents 
| where StartTime > datetime(2007-06-04) and StartTime < datetime(2007-06-10) 
| where Source in ("Source","Public","Emergency Manager","Trained Spotter","Law Enforcement")
| summarize count() by bin(StartTime, 10h), Source
```

:::image type="content" source="images/tour/090.png" alt-text="090":::

Il suffit d’ajouter le `| render timechart`terme de rendu à ce qui précède: .

:::image type="content" source="images/tour/100.png" alt-text="100":::

Remarquez `render timechart` que utilise la première colonne comme x-axe, puis affiche les autres colonnes comme lignes séparées.

## <a name="daily-average-cycle"></a>Cycle moyen quotidien

Comment l’activité varie-t-elle au cours de la journée moyenne?

Comptez les événements au moment modulo un jour, binned en heures. Notez que `floor` nous utilisons au lieu de bin:

```kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

:::image type="content" source="images/tour/120.png" alt-text="120":::

Actuellement, `render` n’étiquette pas les durées correctement, mais nous pourrions utiliser `| render columnchart` à la place:

:::image type="content" source="images/tour/110.png" alt-text="110":::

## <a name="compare-multiple-daily-series"></a>Comparer plusieurs séries quotidiennes

Comment l’activité varie-t-elle au fil de la journée dans différents États?

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

:::image type="content" source="images/tour/130.png" alt-text="130":::

Divisez `1h` par pour transformer l’axe x en nombre d’heures au lieu d’une durée:

```kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)/ 1h
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render columnchart
```

:::image type="content" source="images/tour/140.png" alt-text="140":::

## <a name="join"></a>join

Comment trouver pour deux eventTypes donné dans quel état les deux s’est passé?

Vous pouvez tirer des événements orageux avec le premier EventType et avec le deuxième EventType, puis rejoindre les deux ensembles sur State.

```kusto
StormEvents
| where EventType == "Lightning"
| join (
    StormEvents 
    | where EventType == "Avalanche"
) on State  
| distinct State
```

:::image type="content" source="images/tour/145.png" alt-text="145":::

## <a name="user-session-example-of-join"></a>Exemple de session utilisateur de l’adhésion

Cette section n’utilise pas la `StormEvents` table.

Supposons que vous avez des données qui incluent des événements marquant le début et la fin de chaque session utilisateur, avec un ID unique pour chaque session. 

Combien de temps dure chaque session utilisateur ?

En `extend` utilisant pour fournir un alias pour les deux timestamps, vous pouvez ensuite calculer la durée de la session.

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

:::image type="content" source="images/tour/150.png" alt-text="150":::

Avant d’effectuer la jointure, nous pouvons utiliser `project` pour sélectionner uniquement les colonnes dont nous avons besoin.
Dans les mêmes clauses, nous renommons la colonne timestamp.

## <a name="plot-a-distribution"></a>Tracer une distribution

Combien y a-t-il de tempêtes de longueurs différentes?

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

:::image type="content" source="images/tour/170.png" alt-text="170":::

Ou `| render columnchart`utilisez :

:::image type="content" source="images/tour/160.png" alt-text="160":::

## <a name="percentiles"></a>Centiles

Quelles sont les durées qui couvrent différents pourcentages de tempêtes?

Utilisez la requête ci-dessus, mais remplacez par `render` :

```kusto
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

Dans ce cas, `by` nous n’avons fourni aucune clause, de sorte que le résultat est une seule ligne:

:::image type="content" source="images/tour/180.png" alt-text="180":::

Nous pouvons voir que :

* 5% des tempêtes ont une durée inférieure à 5m;
* 50% des tempêtes durent moins de 1h 25m;
* 5% des orages durent au moins 2h 50m.

Pour obtenir une ventilation distincte pour chaque état, nous avons juste à apporter la colonne d’état séparément par les deux opérateurs de résumé:

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

:::image type="content" source="images/tour/190.png" alt-text="190":::

## <a name="let-assign-a-result-to-a-variable"></a>Let: affecter un résultat à une variable

Utilisez [laisser](./letstatement.md) séparer les parties de l’expression de requête dans l’exemple de «joindre» ci-dessus. Les résultats sont identiques :

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

> Astuce: Dans le client Kusto, ne mettez pas de lignes vides entre les parties de cette. Exécutez-la en totalité.

## <a name="combining-data-from-several-databases-in-a-query"></a>Combiner les données de plusieurs bases de données dans une requête

Voir [les requêtes transversales](./cross-cluster-or-database-queries.md) pour une discussion détaillée

Lorsque vous écrivez une requête du style:

```kusto
Logs | where ...
```

Le tableau nommé Logs doit être dans votre base de données par défaut. Si vous souhaitez accéder aux tables d’une autre base de données, utilisez la syntaxe suivante :

```kusto
database("db").Table
```

Donc, si vous avez des bases de données *nommées Diagnostics* et *Télémétrie* et que vous souhaitez corréler certaines de leurs données, vous pouvez écrire (en supposant que *Diagnostics* est votre base de données par défaut)

```kusto
Logs | join database("Telemetry").Metrics on Request MachineId | ...
```

ou si votre base de données par défaut est *Telemetry*

```kusto
union Requests, database("Diagnostics").Logs | ...
```
    
Tout ce qui précède supposait que les deux bases de données résident dans le cluster à qui vous êtes actuellement connecté. Supposons que la base de données *Telemetry* appartenait à un autre cluster nommé *TelemetryCluster.kusto.windows.net* alors pour y accéder

```kusto
Logs | join cluster("TelemetryCluster").database("Telemetry").Metrics on Request MachineId | ...
```

> Remarque : lorsque le cluster est spécifié, la base de données est obligatoire

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
