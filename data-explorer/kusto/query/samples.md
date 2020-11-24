---
title: Exemples de requêtes dans Azure Explorateur de données et Azure Monitor
description: Cet article décrit des requêtes et des exemples courants qui utilisent le langage de requête Kusto pour Azure Explorateur de données et Azure Monitor.
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
ms.openlocfilehash: 866df577fa039e8b92c31753197cf4a4fa03f139
ms.sourcegitcommit: faa747df81c49b96d173dbd5a28d2ca4f3a2db5f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95783525"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Exemples de requêtes pour Azure Explorateur de données et Azure Monitor

::: zone pivot="azuredataexplorer"

Cet article identifie les besoins courants en matière de requêtes dans Azure Explorateur de données et comment vous pouvez utiliser le langage de requête Kusto pour les satisfaire.

## <a name="display-a-column-chart"></a>Afficher un histogramme

Pour projeter plusieurs colonnes, puis utiliser les colonnes comme axe des abscisses et axe des y d’un graphique :

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* La première colonne forme l’axe des x. Il peut s’agir de valeurs numériques, de date et d’heure ou de chaîne. 
* Utilisez `where` , `summarize` et `top` pour limiter le volume de données que vous affichez.
* Triez les résultats pour définir l’ordre de l’axe x.

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="Capture d’écran d’un histogramme, avec dix colonnes de couleur qui décrivent les valeurs respectives de 10 emplacements.":::

## <a name="get-sessions-from-start-and-stop-events"></a>Obtenir des sessions à partir d’événements de démarrage et d’arrêt

Dans un journal des événements, certains événements marquent le début ou la fin d’une activité ou d’une session étendue. 

|Nom|City|SessionId|Timestamp|
|---|---|---|---|
|Démarrer|London|2817330|2015-12-09T10:12:02.32|
|Jeu|London|2817330|2015-12-09T10:12:52.45|
|Démarrer|Manchester|4267667|2015-12-09T10:14:02.23|
|Arrêter|London|2817330|2015-12-09T10:23:43.18|
|Annuler|Manchester|4267667|2015-12-09T10:27:26.29|
|Arrêter|Manchester|4267667|2015-12-09T10:28:31.72|

Chaque événement a un ID de session ( `SessionId` ). Le défi consiste à faire correspondre les événements de démarrage et d’arrêt avec un ID de session.

Exemple :

```kusto
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on SessionId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

Pour faire correspondre les événements de démarrage et d’arrêt avec un ID de session :

1. Utilisez [Let](./letstatement.md) pour nommer une projection de la table qui est réduite le plus possible avant de commencer la jointure.
1. Utilisez [Project](./projectoperator.md) pour modifier les noms des horodateurs afin que l’heure de début et l’heure d’arrêt s’affichent dans les résultats. `project` sélectionne également les autres colonnes à afficher dans les résultats. 
1. Utilisez [join](./joinoperator.md) pour faire correspondre les entrées de démarrage et d’arrêt de la même activité. Une ligne est créée pour chaque activité. 
1. Réutilisez `project` à nouveau pour ajouter une colonne afin d’afficher la durée de l’activité.

Voici le format :

|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>Obtenir des sessions sans utiliser d’ID de session

Supposons que les événements de démarrage et d’arrêt ne disposent pas d’un ID de session avec lequel nous pouvons faire correspondre. Toutefois, nous avons l’adresse IP du client dans lequel la session a eu lieu. En supposant que chaque adresse de client exécute une seule session à la fois, nous pouvons faire correspondre chaque événement de démarrage à l’événement d’arrêt suivant à partir de la même adresse IP :

Exemple :

```kusto
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize arg_min(duration, *) by bin(StartTime,1s), ClientIp
```

Le `join` correspond à chaque heure de début avec toutes les heures d’arrêt de la même adresse IP du client. L’exemple de code :

- Supprime les correspondances avec des temps d’arrêt antérieurs.
- Groupes par heure de début et adresse IP pour obtenir un groupe pour chaque session. 
- Fournit une `bin` fonction pour le `StartTime` paramètre. Si vous n’effectuez pas cette étape, Kusto utilise automatiquement des emplacements d’une heure qui correspondent à des heures de démarrage avec des heures d’arrêt incorrectes.

`arg_min` recherche la ligne avec la plus petite durée dans chaque groupe, et le `*` paramètre passe par toutes les autres colonnes. 

Préfixes `min_` d’argument pour chaque nom de colonne. 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="Capture d’écran d’une table qui répertorie les résultats, avec des colonnes pour l’heure de début, l’adresse IP du client, la durée, la ville et le point d’arrêt le plus ancien pour chaque combinaison de client/heure de début."::: 

Ajoutez du code pour compter les durées dans des emplacements facilement dimensionnés. Dans cet exemple, en raison d’une préférence pour un graphique à barres, divisez par `1s` pour convertir les intervalles en nombres :

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="Capture d’écran d’un histogramme qui représente le nombre de sessions, avec des durées dans les plages spécifiées.":::

### <a name="full-example"></a>Exemple complet

```kusto
Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"      
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

## <a name="chart-concurrent-sessions-over-time"></a>Sessions simultanées du graphique au fil du temps

Supposons que vous disposiez d’une table d’activités et de leurs heures de début et de fin. Vous pouvez afficher un graphique qui affiche le nombre d’activités exécutées simultanément au fil du temps.

Voici un exemple d’entrée, appelé `X` :

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Pour un graphique dans des emplacements d’une minute, vous souhaitez compter chaque activité en cours d’exécution à chaque intervalle d’une minute.

Voici un résultat intermédiaire :

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` génère un tableau de valeurs à des intervalles spécifiés :

|SessionId | StartTime | StopTime  | exemples|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Au lieu de conserver ces tableaux, développez-les à l’aide de [MV-Expand](./mvexpandoperator.md):

```kusto
X | mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
```

|SessionId | StartTime | StopTime  | exemples|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | 10:01:00|
| a | 10:01:33 | 10:06:31 | 10:02:00|
| a | 10:01:33 | 10:06:31 | 10:03:00|
| a | 10:01:33 | 10:06:31 | 10:04:00|
| a | 10:01:33 | 10:06:31 | 10:05:00|
| a | 10:01:33 | 10:06:31 | 10:06:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|

À présent, regroupez les résultats par heure d’échantillonnage et comptez les occurrences de chaque activité :

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Utilisez `todatetime()` , car [MV-Expand](./mvexpandoperator.md) génère une colonne de type dynamique.
* Utilisez, `bin()` car, pour les valeurs numériques et les dates, si vous ne fournissez pas d’intervalle, `summarize` applique toujours une `bin()` fonction à l’aide d’un intervalle par défaut. 

Voici le format :

| count_SessionId | exemples|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Vous pouvez utiliser un graphique à barres ou un graphique temporel pour afficher les résultats.

## <a name="introduce-null-bins-into-summarize"></a>Introduire des emplacements null dans le *Résumé*

Lorsque l' `summarize` opérateur est appliqué sur une clé de groupe qui se compose d’une colonne de date et d’heure, placez ces valeurs dans des emplacements à largeur fixe :

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

Cet exemple produit une table qui comporte une seule ligne par groupe de lignes dans `T` chaque emplacement de cinq minutes.

Ce que le code ne fait pas est ajouter « emplacements null » — lignes pour les valeurs de l’intervalle de temps entre `StartTime` et `StopTime` pour lesquelles il n’y a pas de ligne correspondante dans `T` . Il est judicieux de « remplir » la table avec ces emplacements. Voici une façon de procéder :

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| summarize Count=count() by bin(Timestamp, 5m)
| where ...
| union ( // 1
  range x from 1 to 1 step 1 // 2
  | mv-expand Timestamp=range(StartTime, StopTime, 5m) to typeof(datetime) // 3
  | extend Count=0 // 4
  )
| summarize Count=sum(Count) by bin(Timestamp, 5m) // 5 
```

Voici une explication pas à pas de la requête précédente : 

1. Utilisez l' `union` opérateur pour ajouter des lignes à une table. Ces lignes sont produites par l' `union` expression.
1. L' `range` opérateur produit une table qui comporte une seule ligne et une seule colonne. La table n’est utilisée pour aucune autre chose que pour `mv-expand` le travail sur.
1. L' `mv-expand` opérateur sur la `range` fonction crée autant de lignes qu’il y a de casiers de cinq minutes entre `StartTime` et `EndTime` .
1. Utilisez un `Count` de `0` .
1. L' `summarize` opérateur groupe les emplacements de l’argument d’origine (gauche ou externe) à `union` . L’opérateur est également un emplacement de l’argument interne (lignes bin null). Ce processus garantit que la sortie comporte une ligne par emplacement dont la valeur est égale à zéro ou au nombre d’origine.

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>Tirez davantage de vos données à l’aide de Kusto avec Machine Learning 

De nombreux cas d’usage intéressants utilisent des algorithmes de Machine Learning et dérivent des Insights intéressants à partir de données de télémétrie. Souvent, ces algorithmes nécessitent un DataSet strictement structuré comme entrée. En règle générale, les données brutes du journal ne correspondent pas à la structure et à la taille requises. 

Commencez par Rechercher les anomalies dans le taux d’erreur d’un service d’inférences Bing spécifique. La table logs contient 65 milliards enregistrements. La requête de base suivante filtre les erreurs 250 000, puis crée une série chronologique de nombre d’erreurs qui utilise la fonction de détection des anomalies [series_decompose_anomalies](series-decompose-anomaliesfunction.md). Les anomalies sont détectées par le service Kusto et sont mises en surbrillance en tant que points rouges sur le graphique de série chronologique.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Le service a identifié quelques compartiments de temps qui avaient des taux d’erreur suspects. Utilisez Kusto pour effectuer un zoom avant sur ce laps de temps. Exécutez ensuite une requête qui agrège sur la `Message` colonne. Essayez de trouver les erreurs principales. 

Les parties pertinentes de l’ensemble de la trace de la pile du message sont découpées, de sorte que les résultats s’adaptent mieux à la page. 

Vous pouvez voir la réussite de l’identification des huit principales erreurs. Toutefois, Next est une série longue d’erreurs, car le message d’erreur a été créé à l’aide d’une chaîne de format qui contenait des données modifiées :

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| summarize count() by Message 
| top 10 by count_ 
| project count_, Message 
```

|count_|Message
|---|---
|7125|Échec de ExecuteAlgorithmMethod pour la méthode « RunCycleFromInterimData »...
|7125|Appel InferenceHostService failed..SysTEM. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet...
|7124|Système inférence inattendue error..SysTEM. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet... 
|5112|Système inférence inattendue error..SysTEM. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet.
|174|InferenceHostService appelez failed..SysTEM. ServiceModel. CommunicationException : une erreur s’est produite lors de l’écriture dans le canal :...
|10|Échec de ExecuteAlgorithmMethod pour la méthode « RunCycleFromInterimData »...
|10|Erreur système d’inférence.. Microsoft. Bing. Platform. inférences. service. managers. UserInterimDataManagerException :...
|3|Appel InferenceHostService failed..SysTEM. ServiceModel. CommunicationObjectFaultedException :...
|1|Erreur système d’inférence... SocialGraph. patron. OperationResponse... AIS TraceId : 8292FC561AC64BED8FA243808FE74EFD...
|1|Erreur système d’inférence... SocialGraph. patron. OperationResponse... AIS TraceId : 5F79F7587FF943EC9B641E02E701AFBF...

À ce stade, l’utilisation de l' `reduce` opérateur vous aide. L’opérateur a identifié 63 Erreurs différentes qui proviennent du même point d’instrumentation de trace dans le code. `reduce` permet de se concentrer sur des traces d’erreurs plus explicites dans cette fenêtre de temps.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Count|Modèle
|---|---
|7125|Échec de ExecuteAlgorithmMethod pour la méthode « RunCycleFromInterimData »...
|  7125|Appel InferenceHostService failed..SysTEM. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet...
|  7124|Système inférence inattendue error..SysTEM. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet...
|  5112|Système inférence inattendue error..SysTEM. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet...
|  174|InferenceHostService appelez failed..SysTEM. ServiceModel. CommunicationException : une erreur s’est produite lors de l’écriture dans le canal :...
|  63|Erreur système d’inférence.. Microsoft. Bing. Platform. inférences. \* : écrire \* pour écrire dans le patron de l’objet. \* : SocialGraph. patron. Request...
|  10|Échec de ExecuteAlgorithmMethod pour la méthode « RunCycleFromInterimData »...
|  10|Erreur système d’inférence.. Microsoft. Bing. Platform. inférences. service. managers. UserInterimDataManagerException :...
|  3|Appel InferenceHostService failed..SysTEM. ServiceModel. \* : l’objet, System. ServiceModel. Channels. \* + \* , pour \* \* est le \* ... à edémarrage...

Maintenant, vous avez une bonne vue des erreurs principales qui ont contribué aux anomalies détectées.

Pour comprendre l’effet de ces erreurs dans l’exemple de système, tenez compte des éléments suivants : 
* La `Logs` table contient des données dimensionnelles supplémentaires, comme `Component` et `Cluster` .
* Le nouveau plug-in de clusters autocluster peut aider à dériver les détails des composants et des clusters avec une requête simple. 

Dans l’exemple suivant, vous pouvez voir clairement que chacune des quatre erreurs principales est spécifique à un composant. En outre, bien que les trois erreurs principales soient spécifiques au cluster est renommé db4, la quatrième erreur se produit sur tous les clusters.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Count |Pourcentage (%)|Composant|Cluster|Message
|---|---|---|---|---
|7125|26,64|InferenceHostService|EST renommé db4|ExecuteAlgorithmMethod pour la méthode...
|7125|26,64|Composant inconnu|EST renommé db4|Échec de l’appel de InferenceHostService...
|7124|26,64|InferenceAlgorithmExecutor|EST renommé db4|Erreur système inattendue...
|5112|19,11|InferenceAlgorithmExecutor|*|Erreur système inattendue...

## <a name="map-values-from-one-set-to-another"></a>Mapper des valeurs d’un jeu à un autre

Un cas d’utilisation de requête courant est un mappage statique de valeurs. Le mappage statique peut aider à rendre les résultats plus présents.

Par exemple, dans la table suivante, `DeviceModel` spécifie un modèle d’appareil. L’utilisation du modèle d’appareil n’est pas une forme pratique de référencement du nom de l’appareil.  

|DeviceModel |Count 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

 L’utilisation d’un nom convivial est plus pratique :  

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Les deux exemples suivants montrent comment passer de l’utilisation d’un modèle d’appareil à un nom convivial pour identifier un appareil.  

### <a name="map-by-using-a-dynamic-dictionary"></a>Mapper à l’aide d’un dictionnaire dynamique

Vous pouvez obtenir un mappage en utilisant un dictionnaire dynamique et des accesseurs dynamiques. Par exemple :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Dataset definition
let Source = datatable(DeviceModel:string, Count:long)
[
  'iPhone5,1', 32,
  'iPhone3,2', 432,
  'iPhone7,2', 55,
  'iPhone5,2', 66,
];
// Query start here
let phone_mapping = dynamic(
  {
    "iPhone5,1" : "iPhone 5",
    "iPhone3,2" : "iPhone 4",
    "iPhone7,2" : "iPhone 6",
    "iPhone5,2" : "iPhone5"
  });
Source 
| project FriendlyName = phone_mapping[DeviceModel], Count
```

|FriendlyName|Count|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone5|66|

### <a name="map-by-using-a-static-table"></a>Mapper à l’aide d’une table statique

Vous pouvez également obtenir un mappage à l’aide d’une table persistante et d’un `join` opérateur.
 
1. Créez la table de mappage une seule fois :

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. Créez un tableau du contenu de l’appareil :

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5, 1 |iPhone 5 
    |iPhone3, 2 |iPhone 4 
    |iPhone7, 2 |iPhone 6 
    |iPhone5, 2 |iPhone5 

1. Créer une source de table de test :

    ```kusto
    .create table Source (DeviceModel: string, Count: int)

    .ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
    ```

1. Joindre les tables et exécuter le projet :

   ```kusto
   Devices  
   | join (Source) on DeviceModel  
   | project FriendlyName, Count
   ```

Voici le format :

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Créer et utiliser des tables de dimension de temps de requête

Souvent, vous souhaiterez joindre les résultats d’une requête avec une table de dimension ad hoc qui n’est pas stockée dans la base de données. Vous pouvez définir une expression dont le résultat est une table dont l’étendue est limitée à une seule requête. 

Par exemple :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// Create a query-time dimension table using datatable
let DimTable = datatable(EventType:string, Code:string)
  [
    "Heavy Rain", "HR",
    "Tornado",    "T"
  ]
;
DimTable
| join StormEvents on EventType
| summarize count() by Code
```

Voici un exemple légèrement plus complexe :

```kusto
// Create a query-time dimension table using datatable
let TeamFoundationJobResult = datatable(Result:int, ResultString:string)
  [
    -1, 'None', 0, 'Succeeded', 1, 'PartiallySucceeded', 2, 'Failed',
    3, 'Stopped', 4, 'Killed', 5, 'Blocked', 6, 'ExtensionNotFound',
    7, 'Inactive', 8, 'Disabled', 9, 'JobInitializationError'
  ]
;
JobHistory
  | where PreciseTimeStamp > ago(1h)
  | where Service  != "AX"
  | where Plugin has "Analytics"
  | sort by PreciseTimeStamp desc
  | join kind=leftouter TeamFoundationJobResult on Result
  | extend ExecutionTimeSpan = totimespan(ExecutionTime)
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage
```

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>Récupérer les enregistrements les plus récents (par horodateur) par identité

Supposons que vous disposiez d’une table qui comprend les éléments suivants :
* `ID`Colonne qui identifie l’entité à laquelle chaque ligne est associée, telle qu’un ID d’utilisateur ou un ID de nœud.
* `timestamp`Colonne qui fournit la référence d’heure pour la ligne
* D’autres colonnes

Vous pouvez utiliser l' [opérateur Top imbriqué](topnestedoperator.md) pour effectuer une requête qui retourne les deux derniers enregistrements pour chaque valeur de la `ID` colonne, où la _dernière_ valeur est définie comme _`timestamp` ayant la valeur la plus élevée_:

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // #1
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // #2
  top-nested 2 of timestamp by dummy1=max(timestamp),          // #3
  top-nested   of bla       by dummy2=max(1)                   // #4
| project-away dummy0, dummy1, dummy2                          // #5
```


Voici une explication pas à pas de la requête précédente (la numérotation fait référence aux chiffres dans les commentaires de code) :

1. Le `datatable` est un moyen de générer des données de test à des fins de démonstration. Normalement, vous utilisez des données réelles ici.
1. Cette ligne signifie essentiellement _retourner toutes les valeurs distinctes `id` de_. 
1. Cette ligne retourne ensuite, pour les deux premiers enregistrements qui maximisent :
     * La `timestamp` colonne
     * Les colonnes du niveau précédent (ici, juste `id` )
     * Colonne spécifiée à ce niveau (ici, `timestamp` )
1. Cette ligne ajoute les valeurs de la `bla` colonne pour chacun des enregistrements retournés par le niveau précédent. Si la table contient d’autres colonnes qui vous intéressent, vous pouvez répéter cette ligne pour chacune de ces colonnes.
1. La dernière ligne utilise l' [opérateur Project-Away](projectawayoperator.md) pour supprimer les colonnes « supplémentaires » introduites par `top-nested` .

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>Étendre une table d’un pourcentage du calcul total

Une expression tabulaire qui comprend une colonne numérique est plus utile pour l’utilisateur lorsqu’elle est accompagnée de sa valeur sous la forme d’un pourcentage du total.

Par exemple, supposons qu’une requête génère la table suivante :

|SomeSeries|SomeInt|
|----------|-------|
|Apple       |    100|
|Banane       |    200|

Vous souhaitez afficher la table comme suit :

|SomeSeries|SomeInt|Remise |
|----------|-------|----|
|Apple       |    100|33,3|
|Banane       |    200|66,6|

Pour modifier le mode d’affichage du tableau, calculez le total (somme) de la `SomeInt` colonne, puis divisez chaque valeur de cette colonne par le total. Pour obtenir des résultats arbitraires, utilisez l' [opérateur as](asoperator.md).

Par exemple :

```kusto
// The following table literally represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Apple",
  200, "Banana",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>Effectuer des agrégations sur une fenêtre glissante

L’exemple suivant montre comment synthétiser des colonnes à l’aide d’une fenêtre glissante. Pour la requête, utilisez le tableau suivant, qui contient les prix des fruits par horodateurs.

Calculez les coûts minimaux, maximaux et de somme de chaque fruit par jour à l’aide d’une fenêtre glissante de sept jours. Chaque enregistrement dans le jeu de résultats regroupe les sept jours précédents et les résultats contiennent un enregistrement par jour dans la période d’analyse.

Table des fruits :

|Timestamp|Fruit|Price|
|---|---|---|
|2018-09-24 21:00:00.0000000|Bananes|3|
|2018-09-25 20:00:00.0000000|Pommes|9|
|2018-09-26 03:00:00.0000000|Bananes|4|
|2018-09-27 10:00:00.0000000|Bouclé|8|
|2018-09-28 07:00:00.0000000|Bananes|6|
|2018-09-29 21:00:00.0000000|Bananes|8|
|2018-09-30 01:00:00.0000000|Bouclé|2|
|2018-10-01 05:00:00.0000000|Bananes|0|
|2018-10-02 02:00:00.0000000|Bananes|0|
|2018-10-03 13:00:00.0000000|Bouclé|4|
|2018-10-04 14:00:00.0000000|Pommes|8|
|2018-10-05 05:00:00.0000000|Bananes|2|
|2018-10-06 08:00:00.0000000|Bouclé|8|
|2018-10-07 12:00:00.0000000|Bananes|0|

Voici la requête d’agrégation de fenêtre glissante. Consultez l’explication après le résultat de la requête.

```kusto
let _start = datetime(2018-09-24);
let _end = _start + 13d; 
Fruits 
| extend _bin = bin_at(Timestamp, 1d, _start) // #1 
| extend _endRange = iif(_bin + 7d > _end, _end, 
                            iff( _bin + 7d - 1d < _start, _start,
                                iff( _bin + 7d - 1d < _bin, _bin,  _bin + 7d - 1d)))  // #2
| extend _range = range(_bin, _endRange, 1d) // #3
| mv-expand _range to typeof(datetime) limit 1000000 // #4
| summarize min(Price), max(Price), sum(Price) by Timestamp=bin_at(_range, 1d, _start) ,  Fruit // #5
| where Timestamp >= _start + 7d; // #6

```

Voici le format :

|Timestamp|Fruit|min_Price|max_Price|sum_Price|
|---|---|---|---|---|
|2018-10-01 00:00:00.0000000|Pommes|9|9|9|
|2018-10-01 00:00:00.0000000|Bananes|0|8|18|
|2018-10-01 00:00:00.0000000|Bouclé|2|8|10|
|2018-10-02 00:00:00.0000000|Bananes|0|8|18|
|2018-10-02 00:00:00.0000000|Bouclé|2|8|10|
|2018-10-03 00:00:00.0000000|Bouclé|2|8|14|
|2018-10-03 00:00:00.0000000|Bananes|0|8|14|
|2018-10-04 00:00:00.0000000|Bananes|0|8|14|
|2018-10-04 00:00:00.0000000|Bouclé|2|4|6|
|2018-10-04 00:00:00.0000000|Pommes|8|8|8|
|2018-10-05 00:00:00.0000000|Bananes|0|8|10|
|2018-10-05 00:00:00.0000000|Bouclé|2|4|6|
|2018-10-05 00:00:00.0000000|Pommes|8|8|8|
|2018-10-06 00:00:00.0000000|Bouclé|2|8|14|
|2018-10-06 00:00:00.0000000|Bananes|0|2|2|
|2018-10-06 00:00:00.0000000|Pommes|8|8|8|
|2018-10-07 00:00:00.0000000|Bananes|0|2|2|
|2018-10-07 00:00:00.0000000|Bouclé|4|8|12|
|2018-10-07 00:00:00.0000000|Pommes|8|8|8|

La requête « s’étire » (duplique) chaque enregistrement dans la table d’entrée pendant les sept jours suivant son apparence réelle. Chaque enregistrement apparaît en réalité sept fois. Par conséquent, l’agrégation quotidienne comprend tous les enregistrements des sept jours précédents.


Voici une explication pas à pas de la requête précédente : 

1. Compartimenter chaque enregistrement à un jour (par rapport à `_start` ). 
1. Déterminez la fin de la plage par enregistrement : `_bin + 7d` , sauf si la valeur est en dehors de la plage de `_start` et `_end` , dans ce cas, elle est ajustée. 
1. Pour chaque enregistrement, créez un tableau de sept jours (horodateurs), en commençant au jour de l’enregistrement actuel. 
1. `mv-expand` le tableau, en dupliquant chaque enregistrement dans sept enregistrements, un jour indépendamment l’un de l’autre. 
1. Exécutez la fonction d’agrégation pour chaque jour. En raison de #4, cette étape résume en fait _les sept derniers jours_ . 
1. Les données des sept premiers jours sont incomplètes, car il n’y a pas de période de lookback de sept jours pour les sept premiers jours. Les sept premiers jours sont exclus du résultat final. Dans l’exemple, ils participent uniquement à l’agrégation pour 2018-10-01.

## <a name="find-the-preceding-event"></a>Rechercher l’événement précédent

L’exemple suivant montre comment rechercher un événement précédent entre deux jeux de données.  

Vous avez deux jeux de données, A et B. Pour chaque enregistrement dans le jeu de données B, recherchez son événement précédent dans le jeu de données A (autrement dit, l' `arg_max` enregistrement d’un qui est encore _plus ancien_ que B).

Voici les exemples de jeux de données : 

```kusto
let A = datatable(Timestamp:datetime, ID:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, ID:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|Timestamp|id|EventB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|o|Ay1|
|2019-01-01 00:00:05.0000000|o|Ay2|

</br>

|Timestamp|id|Événement|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|o|B|
|2019-01-01 00:02:00.0000000|z|B|

Sortie attendue : 

|id|Timestamp|EventB|A_Timestamp|Événement|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|o|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Nous vous recommandons d’utiliser deux approches différentes pour résoudre ce problème. Vous pouvez tester à la fois sur votre jeu de données spécifique afin de trouver celui qui convient le mieux à votre scénario.

> [!NOTE] 
> Chaque approche peut s’exécuter différemment sur des jeux de données différents.

### <a name="approach-1"></a>Approche 1

Cette approche sérialise les deux jeux de données par ID et horodateur. Ensuite, il regroupe tous les événements du DataSet B avec tous leurs événements précédents dans le jeu de données A. Enfin, il récupère l' `arg_max` ensemble des événements du DataSet A dans le groupe.

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by ID, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != ID), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, ID
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="approach-2"></a>Approche 2

Cette approche pour résoudre le problème requiert une période lookback maximale. _L’approche examine l’ampleur de_ l’enregistrement du jeu de données A par rapport au jeu de données B. La méthode joint ensuite les deux jeux de données en fonction de l’ID et de cette période de lookback.

Le `join` produit tous les candidats possibles, tous les enregistrements de jeu de données A qui sont plus anciens que les enregistrements dans le jeu de données B et au cours de la période lookback. Ensuite, le plus proche du jeu de données B est filtré par `arg_min (TimestampB - TimestampA)` . Plus la période lookback est petite, plus les résultats de la requête sont performants.

Dans l’exemple suivant, la période lookback est définie sur `1m` . L’enregistrement avec l’ID `z` n’a pas d' `A` événement correspondant, car son `A` événement est plus ancien de deux minutes.

```kusto 
let _maxLookbackPeriod = 1m;  
let _internalWindowBin = _maxLookbackPeriod / 2;
let B_events = B 
    | extend ID = new_guid()
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend B_Timestamp = Timestamp, _range;
let A_events = A 
    | extend _time = bin(Timestamp, _internalWindowBin)
    | extend _range = range(_time - _internalWindowBin, _time + _maxLookbackPeriod, _internalWindowBin) 
    | mv-expand _range to typeof(datetime) 
    | extend A_Timestamp = Timestamp, _range;
B_events
    | join kind=leftouter (
        A_events
) on ID, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project ID, B_Timestamp, A_Timestamp, EventB, EventA
```

|id|B_Timestamp|A_Timestamp|EventB|Événement|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|o|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||


## <a name="next-steps"></a>Étapes suivantes

- Passez en revue un [didacticiel sur le langage de requête Kusto](tutorial.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

Cet article identifie les besoins courants en matière de requêtes dans Azure Monitor et comment vous pouvez utiliser le langage de requête Kusto pour les satisfaire.

## <a name="string-operations"></a>Opérations de chaîne

Les sections suivantes fournissent des exemples montrant comment utiliser des chaînes lors de l’utilisation du langage de requête Kusto.

### <a name="strings-and-how-to-escape-them"></a>Chaînes et comment les placer dans une séquence d’échappement

Les valeurs de chaîne sont entourées d’apostrophes ou de guillemets doubles. Ajoutez la barre oblique inverse ( \\ ) à gauche d’un caractère pour placer le caractère dans une séquence d’échappement : `\t` pour tabulation, `\n` pour saut de ligne et `\"` pour le caractère guillemet simple.

```kusto
print "this is a 'string' literal in double \" quotes"
```

```kusto
print 'this is a "string" literal in single \' quotes'
```

Pour empêcher « \\ » d’agir comme un caractère d’échappement, ajoutez « \@ » en tant que préfixe à la chaîne :

```kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>Comparaisons de chaînes

Opérateur       |Description                         |Respect de la casse|Exemple (génère `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Égal à                              |Oui           |`"aBc" == "aBc"`
`!=`           |Non égal à                          |Oui           |`"abc" != "ABC"`
`=~`           |Égal à                              |Non            |`"abc" =~ "ABC"`
`!~`           |Non égal à                          |Non            |`"aBc" !~ "xyz"`
`has`          |La valeur du côté droit est un terme entier dans la valeur du côté gauche |Non|`"North America" has "america"`
`!has`         |La valeur du côté droit n’est pas un terme complet dans la valeur du côté gauche       |Non            |`"North America" !has "amer"` 
`has_cs`       |La valeur du côté droit est un terme entier dans la valeur du côté gauche |Oui|`"North America" has_cs "America"`
`!has_cs`      |La valeur du côté droit n’est pas un terme complet dans la valeur du côté gauche       |Oui            |`"North America" !has_cs "amer"` 
`hasprefix`    |La valeur du côté droit est un préfixe de terme dans la valeur du côté gauche         |Non            |`"North America" hasprefix "ame"`
`!hasprefix`   |La valeur du côté droit n’est pas un préfixe de terme dans la valeur de gauche     |Non            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |La valeur du côté droit est un préfixe de terme dans la valeur du côté gauche         |Oui            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |La valeur du côté droit n’est pas un préfixe de terme dans la valeur de gauche     |Oui            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |La valeur du côté droit est un suffixe de terme dans la valeur de gauche         |Non            |`"North America" hassuffix "ica"`
`!hassuffix`   |La valeur du côté droit n’est pas un suffixe de terme dans la valeur de gauche     |Non            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |La valeur du côté droit est un suffixe de terme dans la valeur de gauche         |Oui            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |La valeur du côté droit n’est pas un suffixe de terme dans la valeur de gauche     |Oui            |`"North America" !hassuffix_cs "icA"`
`contains`     |La valeur du côté droit se produit sous la forme d’une sous-séquence de la valeur côté gauche  |Non            |`"FabriKam" contains "BRik"`
`!contains`    |La valeur du côté droit ne se produit pas dans la valeur de gauche           |Non            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |La valeur du côté droit se produit sous la forme d’une sous-séquence de la valeur côté gauche  |Oui           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |La valeur du côté droit ne se produit pas dans la valeur de gauche           |Oui           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |La valeur de droite est une sous-séquence initiale de la valeur de gauche|Non            |`"Fabrikam" startswith "fab"`
`!startswith`  |La valeur du côté droit n’est pas une sous-séquence initiale de la valeur de gauche|Non        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |La valeur de droite est une sous-séquence initiale de la valeur de gauche|Oui            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |La valeur du côté droit n’est pas une sous-séquence initiale de la valeur de gauche|Oui        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |La valeur de droite est une sous-séquence de fermeture de la valeur du côté gauche|Non             |`"Fabrikam" endswith "Kam"`
`!endswith`    |La valeur du côté droit n’est pas une sous-séquence de fermeture de la valeur du côté gauche|Non         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |La valeur de droite est une sous-séquence de fermeture de la valeur du côté gauche|Oui             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |La valeur du côté droit n’est pas une sous-séquence de fermeture de la valeur du côté gauche|Oui         |`"Fabrikam" !endswith "brik"`
`matches regex`|La valeur du côté gauche contient une correspondance pour la valeur du côté droit|Oui           |`"Fabrikam" matches regex "b.*k"`
`in`           |Égal à l’un des éléments       |Oui           |`"abc" in ("123", "345", "abc")`
`!in`          |N’est égal à aucun des éléments   |Oui           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*countof*

Compte les occurrences d’une sous-chaîne dans une chaîne. Peut faire correspondre des chaînes brutes ou utiliser une expression régulière (Regex). Les correspondances de chaînes brutes peuvent se chevaucher, mais les correspondances Regex ne se chevauchent pas.

```
countof(text, search [, kind])
```

- `text`: Chaîne d’entrée 
- `search`: Chaîne simple ou Regex à faire correspondre au texte
- `kind`: _normal_  |  _Regex_ normal (valeur par défaut : normal).

Retourne le nombre de fois où la chaîne de recherche peut être mise en correspondance dans le conteneur. Les correspondances de chaînes brutes peuvent se chevaucher, mais les correspondances Regex ne se chevauchent pas.

#### <a name="plain-string-matches"></a>Correspondances de chaînes simples

```kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Correspondances d’expression régulière

```kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>*extract*

Obtient une correspondance pour une expression régulière d’une chaîne spécifique. Éventuellement, peut convertir la sous-chaîne extraite dans le type spécifié.

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex`: Expression régulière.
- `captureGroup`: Constante entière positive qui indique le groupe de capture à extraire. Utilisez 0 pour la correspondance entière, 1 pour la valeur correspondant à la première parenthèse \( \) dans l’expression régulière, et 2 ou plus pour les parenthèses suivantes.
- `text` -Chaîne à rechercher.
- `typeLiteral` -Un littéral de type facultatif (par exemple, `typeof(long)` ). Si elle est fournie, la sous-chaîne extraite est convertie dans ce type.

Retourne la sous-chaîne correspondant au groupe de capture indiqué `captureGroup` , éventuellement convertie en `typeLiteral` . Si aucune correspondance n’est trouvée ou si la conversion de type échoue, retourne la valeur null.

L’exemple suivant extrait le dernier octet de `ComputerIP` à partir d’un enregistrement de pulsation :

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

L’exemple suivant extrait le dernier octet, le convertit en type *réel* (nombre), puis calcule la valeur IP suivante :

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Dans l’exemple suivant, `Trace` une définition de est recherchée dans la chaîne `Duration` . La correspondance est convertie `real` en et multipliée par une constante d’heure (1 s), qui est ensuite convertie en `Duration` type `timespan` .

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*IsEmpty*, *IsNotEmpty*, *notentant*

- `isempty` retourne `true` si l’argument est une chaîne vide ou null (consultez `isnull` ).
- `isnotempty` retourne `true` si l’argument n’est pas une chaîne vide ou a la valeur null (consultez `isnotnull` ). Alias : `notempty` .


```kusto
isempty(value)
isnotempty(value)
```

#### <a name="example"></a>Exemple

```kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>*ParseURL*

Fractionne une URL en plusieurs parties, telles que le protocole, l’hôte et le port, puis retourne un objet dictionnaire qui contient les parties en tant que chaînes.

```
parseurl(urlstring)
```

#### <a name="example"></a>Exemple

```kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Voici le format :

```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```

### <a name="replace"></a>*replace*

Remplace toutes les correspondances d’expression régulière par une autre chaîne. 

```
replace(regex, rewrite, input_text)
```

- `regex`: Expression régulière à faire correspondre. Il peut contenir des groupes de capture entre parenthèses \( \) .
- `rewrite`: Regex de remplacement pour toute correspondance faite en faisant correspondre une expression régulière. Utilisez \ 0 pour faire référence à la correspondance entière, \ 1 pour le premier groupe de capture, \ 2, et ainsi de suite, pour les groupes de capture suivants.
- `input_text`: Chaîne d’entrée dans laquelle effectuer la recherche.

Retourne le texte après le remplacement de toutes les correspondances d’expressions régulières par des évaluations de réécriture. Les correspondances ne se chevauchent pas.

#### <a name="example"></a>Exemple

```kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Voici le format :

Activité                                        |Remplacé
------------------------------------------------|----------------------------------------------------------
4663 - Une tentative d’accès à un objet a été effectuée  |ID d’activité 4663 : Une tentative d’accès à un objet a été effectuée.


### <a name="split"></a>*split*

Fractionne une chaîne spécifique en fonction d’un délimiteur spécifié, puis retourne un tableau des sous-chaînes résultantes.

```
split(source, delimiter [, requestedIndex])
```

- `source`: Chaîne à fractionner selon le délimiteur spécifié.
- `delimiter`: Délimiteur qui sera utilisé pour fractionner la chaîne source.
- `requestedIndex`: Index de base zéro facultatif. S’il est fourni, le tableau de chaînes retourné contient uniquement cet élément (s’il existe).


#### <a name="example"></a>Exemple

```kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>*strcat*

Concatène les arguments de chaîne (prend en charge 1 à 16 arguments).

```
strcat("string1", "string2", "string3")
```

#### <a name="example"></a>Exemple

```kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>*strlen*

Retourne la longueur d'une chaîne.

```
strlen("text_to_evaluate")
```

#### <a name="example"></a>Exemple

```kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>*substring*

Extrait une sous-chaîne d’une chaîne source spécifique, en commençant à l’index spécifié. Si vous le souhaitez, vous pouvez spécifier la longueur de la sous-chaîne demandée.

```
substring(source, startingIndex [, length])
```

- `source`: Chaîne source à partir de laquelle la sous-chaîne est extraite.
- `startingIndex`: Position de caractère de départ de base zéro de la sous-chaîne demandée.
- `length`: Paramètre facultatif que vous pouvez utiliser pour spécifier la longueur demandée de la sous-chaîne retournée.

#### <a name="example"></a>Exemple

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*ToLower*, *ToUpper*

Convertit une chaîne spécifique en minuscules ou en majuscules.

```
tolower("value")
toupper("value")
```

#### <a name="example"></a>Exemple

```kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>Opérations Date/heure

Les sections suivantes fournissent des exemples montrant comment utiliser les valeurs de date et d’heure lors de l’utilisation du langage de requête Kusto.

### <a name="date-time-basics"></a>Notions de base sur la date et l’heure

Le langage de requête Kusto comporte deux types de données principaux associés aux dates et heures : `datetime` et `timespan` . Toutes les dates sont exprimées au format UTC. Bien que plusieurs formats de date et d’heure soient pris en charge, le format ISO-8601 est préféré. 

Les types timespan sont exprimés en tant que valeur décimale suivie d’une unité de temps :

|Pratique   | Unité de temps    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | milliseconde  |
|microseconde | microseconde  |
|graduation        | nanoseconde   |

Vous pouvez créer des valeurs de date et d’heure en castant une chaîne à l’aide de l' `todatetime` opérateur. Par exemple, pour passer en revue les pulsations de machine virtuelle envoyées dans un laps de temps spécifique, utilisez l' `between` opérateur pour spécifier un intervalle de temps :

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Un autre scénario courant consiste à comparer une valeur de date et d’heure au présent. Par exemple, pour afficher toutes les pulsations au cours des deux dernières minutes, vous pouvez utiliser l’opérateur `now` avec une valeur timespan qui représente deux minutes :

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Un raccourci est également disponible pour cette fonction :

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

La méthode la plus rapide et la plus lisible utilise l' `ago` opérateur :

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Supposons qu’au lieu de connaître les heures de début et de fin, vous connaissez l’heure de début et la durée. Vous pouvez réécrire la requête :

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>Convertir les unités de temps

Vous souhaiterez peut-être exprimer une valeur de date et d’heure dans une unité de temps autre que la valeur par défaut. Par exemple, si vous examinez les événements d’erreur des 30 dernières minutes et que vous avez besoin d’une colonne calculée qui indique combien de temps l’événement s’est produit, vous pouvez utiliser cette requête :

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

La `timeAgo` colonne contient des valeurs telles que `00:09:31.5118992` , qui sont au format hh : mm : SS. fffffff. Si vous souhaitez mettre en forme ces valeurs à la `number` valeur de minutes depuis l’heure de début, divisez-la par `1m` :

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>Agrégations et création de compartiments par intervalles de temps

Un autre scénario courant est la nécessité d’obtenir des statistiques pour une période spécifique dans une unité de temps spécifique. Pour ce scénario, vous pouvez utiliser un `bin` opérateur dans le cadre d’une `summarize` clause.

Utilisez la requête suivante pour obtenir le nombre d’événements qui se sont produits toutes les cinq minutes au cours de la demi-heure passée :

```kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Cette requête produit le tableau suivant :  

|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Une autre façon de créer des compartiments de résultats consiste à utiliser des fonctions telles que `startofday` :

```kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Voici le format :

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7 136|
|2018-07-29T00:00:00.000|12 315|
|2018-07-30T00:00:00.000|16 847|
|2018-07-31T00:00:00.000|12 616|
|2018-08-01T00:00:00.000|5 416|


### <a name="time-zones"></a>Fuseaux horaires

Étant donné que toutes les valeurs de date et d’heure sont exprimées en UTC, il est souvent utile de convertir ces valeurs dans le fuseau horaire local. Par exemple, utilisez ce calcul pour convertir les heures UTC en PST :

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>Agrégations

Les sections suivantes fournissent des exemples montrant comment agréger les résultats d’une requête lors de l’utilisation du langage de requête Kusto.

### <a name="count"></a>*count*

Comptez le nombre de lignes du jeu de résultats une fois que tous les filtres sont appliqués. L’exemple suivant renvoie le nombre total de lignes de la `Perf` table à partir des 30 dernières minutes. Les résultats sont retournés dans une colonne nommée `count_` , sauf si vous attribuez un nom spécifique à la colonne :


```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Une visualisation graphique temporel peut être utile pour voir une tendance au fil du temps :

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

La sortie de cet exemple montre la `Perf` courbe de tendance du nombre d’enregistrements par intervalles de cinq minutes :


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="Capture d’écran d’un graphique en courbes qui affiche la courbe de tendance du nombre d’enregistrements de performance par intervalles de cinq minutes.":::

### <a name="dcount-dcountif"></a>*CpteDom*, *dcountif*

Utilisez `dcount` et `dcountif` pour compter des valeurs distinctes dans une colonne spécifique. La requête suivante évalue le nombre d’ordinateurs distincts qui ont envoyé des pulsations dans la dernière heure :

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Pour compter uniquement les ordinateurs Linux qui ont envoyé des pulsations, utilisez `dcountif` :

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluate-subgroups"></a>Évaluer les sous-groupes

Pour effectuer un compte ou d’autres agrégations de sous-groupes dans vos données, utilisez le mot clé `by`. Par exemple, pour compter le nombre d’ordinateurs Linux distincts qui ont envoyé des pulsations dans chaque pays ou région, utilisez la requête suivante :

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | ordinateurs_distincts  |
------------------|---------------------|
|États-Unis    | 19                  |
|Canada           | 3                   |
|Irlande          | 0                   |
|Royaume-Uni   | 0                   |
|Pays-Bas      | 2                   |


Pour analyser des sous-groupes encore plus petits de vos données, ajoutez des noms de colonnes à la `by` section. Par exemple, vous souhaiterez peut-être compter les différents ordinateurs de chaque pays ou région par type de système d’exploitation ( `OSType` ) :

```kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>Percentile

Pour rechercher la valeur médiane, utilisez la fonction `percentile` avec une valeur pour spécifier le centile :

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Vous pouvez également spécifier différents centile pour obtenir un résultat agrégé pour chaque :

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Les résultats peuvent indiquer que certains processeurs de l’ordinateur ont des valeurs médianes similaires. Toutefois, bien que certains ordinateurs soient stabilisés autour de la médiane, d’autres ont signalé des valeurs UC plus basses et plus élevées. Les valeurs haute et basse signifient que les ordinateurs ont des pics d’expérience.

### <a name="variance"></a>Variance

Pour évaluer directement la variance d’une valeur, utilisez les méthodes de variance et d’écart type :

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Un bon moyen d’analyser la stabilité de l’utilisation de l’UC consiste à combiner `stdev` avec le calcul médian :

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>Générer des listes et des ensembles

Vous pouvez utiliser `makelist` pour faire pivoter des données selon l’ordre des valeurs dans une colonne spécifique. Par exemple, vous souhaiterez peut-être explorer les événements de commande les plus courants qui se produisent sur vos ordinateurs. Vous pouvez essentiellement faire pivoter les données selon l’ordre des `EventID` valeurs sur chaque ordinateur : 

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

Voici le format :

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` génère une liste dans l’ordre dans lequel les données lui ont été passées. Pour trier les événements du plus ancien au plus récent, utilisez `asc` dans l' `order` instruction au lieu de `desc` . 

Il peut s’avérer utile de créer une liste uniquement de valeurs distinctes. Cette liste est appelée un _ensemble_ et vous pouvez la générer à l’aide de la `makeset` commande :

```kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

Voici le format :

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Comme `makelist` , `makeset` fonctionne également avec les données ordonnées. La `makeset` commande génère des tableaux en fonction de l’ordre des lignes qui lui sont passées.

### <a name="expand-lists"></a>Développer les listes

L’opération inverse de `makelist` ou `makeset` est `mv-expand` . La `mv-expand` commande développe une liste de valeurs pour séparer les lignes. Elle peut s’étendre sur n’importe quel nombre de colonnes dynamiques, y compris les colonnes de tableau et JSON. Par exemple, vous pouvez vérifier la `Heartbeat` table pour les solutions qui ont envoyé des données à partir d’ordinateurs qui ont envoyé une pulsation au cours de la dernière heure :

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

Voici le format :

| Computer | Solutions | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... |

Utilisez `mv-expand` pour afficher chaque valeur dans une ligne distincte plutôt que dans une liste séparée par des virgules :

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
```

Voici le format :

| Computer | Solutions | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "security" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... |


Vous pouvez utiliser `makelist` pour regrouper des éléments. Dans la sortie, vous pouvez voir la liste des ordinateurs par solution :

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mv-expand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

Voici le format :

|Solutions | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

### <a name="missing-bins"></a>Emplacements manquants

Une application utile de `mv-expand` remplit les valeurs par défaut pour les emplacements manquants. Supposons, par exemple, que vous recherchez le temps d’activité d’un ordinateur spécifique en explorant sa pulsation. Vous souhaitez également voir la source de la pulsation, qui se trouve dans la `Category` colonne. Normalement, nous utilisons une instruction de base `summarize` :

```kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

Voici le format :

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent direct | 2017-06-06T17:00:00Z | 15 |
| Agent direct | 2017-06-06T18:00:00Z | 60 |
| Agent direct | 2017-06-06T20:00:00Z | 55 |
| Agent direct | 2017-06-06T21:00:00Z | 57 |
| Agent direct | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Dans la sortie, le compartiment associé à "2017-06-06T19:00:00Z" est manquant, car il n’existe aucune donnée de pulsation pour cette heure. Utilisez la fonction `make-series` pour affecter une valeur par défaut aux compartiments vides. Une ligne est générée pour chaque catégorie. La sortie comprend deux colonnes de tableau supplémentaires, une pour les valeurs et une pour les compartiments de temps correspondants :

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

Voici le format :

| Category | count_ | TimeGenerated |
|---|---|---|
| Agent direct | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Le troisième élément du tableau *count_* est 0, comme prévu. Le tableau _TimeGenerated_ a un horodatage de correspondance de « 2017-06-06T19:00:00.0000000 z ». Toutefois, ce format de tableau est difficile à lire. Utilisez `mv-expand` pour étendre les tableaux et produire le même format de sortie que celui généré par `summarize` :

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mv-expand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

Voici le format :

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent direct | 2017-06-06T17:00:00Z | 15 |
| Agent direct | 2017-06-06T18:00:00Z | 60 |
| Agent direct | 2017-06-06T19:00:00Z | 0 |
| Agent direct | 2017-06-06T20:00:00Z | 55 |
| Agent direct | 2017-06-06T21:00:00Z | 57 |
| Agent direct | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Limiter les résultats à un ensemble d’éléments : *Let*, *MakeSet*, *toscalar*, *in*

Un scénario courant consiste à sélectionner les noms d’entités spécifiques en fonction d’un ensemble de critères, puis à filtrer un jeu de données différent pour ce jeu d’entités. Par exemple, il se peut que vous trouviez des ordinateurs dont les mises à jour sont manquantes et identifient les adresses IP que ces ordinateurs ont appelées à.

Voici un exemple :

```kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>Jointures

Vous pouvez utiliser des jointures pour analyser les données de plusieurs tables dans la même requête. Une jointure fusionne les lignes de deux jeux de données en faisant correspondre les valeurs des colonnes spécifiées.

Voici un exemple :

```kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

Dans l’exemple, le premier DataSet filtre tous les événements de connexion. Ce jeu de données est joint à un deuxième jeu de données qui filtre tous les événements de déconnexion. Les colonnes projetées sont `Computer` ,, `Account` `TargetLogonId` et `TimeGenerated` . Les jeux de données sont corrélés par une colonne partagée, `TargetLogonId` . La sortie est un enregistrement unique par corrélation qui a à la fois la connexion et l’heure de déconnexion.

Si les deux jeux de données ont des colonnes portant le même nom, un numéro d’index est attribué aux colonnes du jeu de données de droite. Dans cet exemple, les résultats s’affichent `TargetLogonId` avec les valeurs de la table de gauche et `TargetLogonId1` avec les valeurs de la table de droite. Dans ce cas, la deuxième `TargetLogonId1` colonne a été supprimée à l’aide de l' `project-away` opérateur.

> [!NOTE]
> Pour améliorer les performances, conservez uniquement les colonnes pertinentes des jeux de données joints à l’aide de l' `project` opérateur.


Utilisez la syntaxe suivante pour joindre deux jeux de données dans lesquels la clé jointe a un nom différent entre les deux tables :

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>Tables de choix

Une utilisation courante des jointures consiste à utiliser `datatable` pour le mappage des valeurs statiques. L’utilisation de `datatable` peut aider à rendre les résultats plus présents. Par exemple, vous pouvez enrichir les données d’événements de sécurité avec le nom de l’événement pour chaque ID d’événement :

```kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

Voici le format :

| eventName | count_ |
|:---|:---|
| Le descripteur d’un objet a été fermé | 290 995 |
| Un handle vers un objet a été demandé | 154 157 |
| Une tentative a été effectuée pour dupliquer un handle vers un objet | 144 305 |
| Une tentative d’accès à un objet a été effectuée | 123 669 |
| Opération de chiffrement | 153 495 |
| Opération de fichier de clé | 153 496 |

## <a name="json-and-data-structures"></a>JSON et les structures de données

Les objets imbriqués sont des objets qui contiennent d’autres objets dans un tableau ou dans une carte de paires clé-valeur. Les objets sont représentés en tant que chaînes JSON. Cette section décrit comment vous pouvez utiliser JSON pour récupérer des données et analyser des objets imbriqués.

### <a name="work-with-json-strings"></a>Utiliser des chaînes JSON

Utilisez `extractjson` pour accéder à un élément JSON spécifique dans un chemin connu. Cette fonction nécessite une expression de chemin d’accès qui utilise les conventions suivantes :

- Utilisez _$_ pour faire référence au dossier racine.
- Utilisez la notation entre crochets ou sous forme de points pour faire référence aux index et aux éléments, comme illustré dans les exemples suivants.


Utilisez des crochets pour les index et des points pour séparer les éléments :

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Cet exemple est similaire, mais il utilise uniquement la notation des crochets :

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Pour un seul élément, vous pouvez utiliser uniquement la notation par points :

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parsejson*

Il est plus facile d’accéder à plusieurs éléments de votre structure JSON sous la forme d’un objet dynamique. Utilisez `parsejson` pour caster des données de texte en objet dynamique. Après avoir converti le code JSON en type dynamique, vous pouvez utiliser des fonctions supplémentaires pour analyser les données.

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```

### <a name="arraylength"></a>*arraylength*

Utilisez `arraylength` pour compter le nombre d’éléments dans un tableau :

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mv-expand"></a>*mv-expand*

Utilisez `mv-expand` pour fractionner les propriétés d’un objet en lignes distinctes :

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mv-expand hosts_object.hosts[0]
```

:::image type="content" source="images/samples/mvexpand-rows.png" alt-text="Capture d’écran montrant hosts_0 avec des valeurs pour l’emplacement, l’état et le taux.":::

### <a name="buildschema"></a>*buildschema*

Utilisez `buildschema` pour obtenir le schéma qui admet toutes les valeurs d’un objet :

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

Le résultat est un schéma au format JSON :

```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```

Le schéma décrit les noms des champs d’objet et leurs types de données correspondants. 

Les objets imbriqués peuvent avoir des schémas différents, comme dans l’exemple suivant :

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>Graphiques

Les sections suivantes fournissent des exemples montrant comment utiliser les graphiques lors de l’utilisation du langage de requête Kusto.

### <a name="chart-the-results"></a>Graphique des résultats

Commencez par examiner le nombre d’ordinateurs par système d’exploitation au cours de la dernière heure :

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Par défaut, les résultats sont affichés sous la forme d’une table :

:::image type="content" source="images/samples/query-results-table.png" alt-text="Capture d’écran qui affiche les résultats de la requête dans une table.":::

Pour obtenir une vue plus utile, sélectionnez **graphique**, puis sélectionnez l’option **secteurs** pour visualiser les résultats :

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="Capture d’écran qui affiche les résultats de la requête dans un graphique à secteurs.":::

### <a name="timecharts"></a>Graphiques temporels

Affichez la moyenne et les 50e et 95e centile de temps processeur dans les emplacements d’une heure. 

La requête suivante génère plusieurs séries. Dans les résultats, vous pouvez choisir la série à afficher dans le graphique temporel.

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Sélectionnez l’option d’affichage graphique en **courbes** :

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="Capture d’écran montrant un graphique en courbes à plusieurs séries.":::

#### <a name="reference-line"></a>Ligne de référence

Une ligne de référence peut vous aider à identifier facilement si la mesure a dépassé un seuil spécifique. Pour ajouter une ligne à un graphique, étendez le jeu de données en ajoutant une colonne constante :

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="Capture d’écran montrant un graphique en courbes à plusieurs séries avec une ligne de référence de seuil.":::


### <a name="multiple-dimensions"></a>Plusieurs dimensions

Plusieurs expressions dans la `by` clause de `summarize` créent plusieurs lignes dans les résultats. Une ligne est créée pour chaque combinaison de valeurs.

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Lorsque vous affichez les résultats sous la forme d’un graphique, le graphique utilise la première colonne de la `by` clause. L’exemple suivant montre un histogramme empilé créé à l’aide de la `EventID` valeur. Les dimensions doivent être de `string` type. Dans cet exemple, la `EventID` valeur est castée en `string` :

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="Capture d’écran montrant un graphique à barres basé sur la colonne EventID.":::

Vous pouvez basculer entre les colonnes en sélectionnant la flèche déroulante du nom de la colonne :

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="Capture d’écran montrant un graphique à barres basé sur la colonne AccountType, avec le sélecteur de colonne visible.":::

## <a name="smart-analytics"></a>Analytique intelligente

Cette section contient des exemples qui utilisent des fonctions Smart Analytics dans Azure Log Analytics pour analyser l’activité des utilisateurs. Vous pouvez utiliser ces exemples pour analyser vos propres applications qui sont surveillées par Azure Application Insights, ou utiliser les concepts de ces requêtes pour effectuer des analyses similaires sur d’autres données. 

### <a name="cohorts-analytics"></a>Analytique des cohortes

L’analyse de cohorte suit l’activité de groupes d’utilisateurs spécifiques, appelés _cohortes_. Cohort Analytics tente de mesurer la manière dont un service est attrayant en mesurant le taux de retour des utilisateurs. Les utilisateurs sont regroupés selon le moment où ils ont utilisé le service pour la première fois. Lors de l’analyse des cohortes, nous nous attendons à observer une baisse d’activité sur les premières périodes suivies. Le titre de chaque cohorte correspond à la semaine durant laquelle ses membres ont été observés pour la première fois.

L’exemple suivant analyse le nombre d’activités effectuées par les utilisateurs pendant cinq semaines après leur première utilisation du service :

```kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user, we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity.
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time.
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after.
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample, we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```

Voici le format :

:::image type="content" source="images/samples/cohorts-table.png" alt-text="Capture d’écran montrant une table de cohortes basée sur l’activité.":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>Cumul des utilisateurs actifs mensuels et de l’adhérence utilisateur

L’exemple suivant utilise l’analyse de série chronologique avec la fonction [series_fir](/azure/kusto/query/series-firfunction) . Vous pouvez utiliser la `series_fir` fonction pour les calculs de fenêtre glissants. L’exemple d’application supervisée est un magasin en ligne qui assure le suivi de l’activité des utilisateurs par le biais d’événements personnalisés. La requête effectue le suivi de deux types d’activités utilisateur : `AddToCart` et `Checkout` . Il définit un utilisateur actif en tant qu’utilisateur qui a effectué une extraction au moins une fois par jour spécifique.

```kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they completed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked out in our website.
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user.
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column that contains a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin.
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day).
| mv-expand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity.
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// Render as timechart.
| render timechart
```

Voici le format :

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="Capture d’écran d’un graphique qui montre le cumul d’utilisateurs actifs par jour sur un mois.":::

L’exemple suivant convertit la requête précédente en fonction réutilisable. L’exemple utilise ensuite la requête pour calculer l’adhérence de l’utilisateur propagé. Un utilisateur actif dans cette requête est défini en tant qu’utilisateur qui a effectué une extraction au moins une fois par jour spécifique.

```kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mv-expand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

Voici le format :

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="Capture d’écran d’un graphique illustrant l’utilisation de l’utilisateur au fil du temps.":::

### <a name="regression-analysis"></a>Analyse de régression

Cet exemple montre comment créer un détecteur automatisé pour les interruptions de service basé exclusivement sur les journaux d’activité des traces d’une application. Le détecteur cherche des traces anormales et soudaines de la quantité relative de suivis d’erreurs et d’avertissements dans l’application.

Deux techniques sont utilisées pour évaluer l’état du service en fonction des données de journaux d’activité des traces :

- Utilisez [make-series](/azure/kusto/query/make-seriesoperator) pour convertir les journaux d’activité des traces textuels semi-structurés en une métrique qui représente le rapport entre les lignes de traces positives et négatives.
- Utilisez [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) et [series_fit_line](/azure/kusto/query/series-fit-linefunction) pour la détection avancée des sauts de ligne à l’aide de l’analyse des séries chronologiques avec une régression linéaire en deux lignes.

```kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day.
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total.
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series.
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series.
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down.
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease).
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>Étapes suivantes

- Passez en revue un [didacticiel sur le langage de requête Kusto](tutorial.md?pivots=azuremonitor).


::: zone-end

