---
title: Exemples de requêtes dans Azure Data Explorer et Azure Monitor
description: Cet article décrit les requêtes et exemples courants utilisant le langage de requête Kusto pour Azure Data Explorer et Azure Monitor.
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
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95783525"
---
# <a name="samples-for-queries-for-azure-data-explorer-and-azure-monitor"></a>Exemples de requêtes pour Azure Data Explorer et Azure Monitor

::: zone pivot="azuredataexplorer"

Cet article identifie les besoins courants en matière de requêtes dans Azure Data Explorer et explique comment utiliser le langage de requête Kusto afin d’y répondre.

## <a name="display-a-column-chart"></a>Afficher un histogramme

Pour projeter plusieurs colonnes, puis utiliser les colonnes en tant qu’axe X et qu’axe Y d’un graphique :

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* La première colonne forme l’axe X. Il peut s’agir de valeurs numériques, de date et d’heure ou de chaîne. 
* Utilisez `where`, `summarize` et `top` pour limiter le volume de données que vous affichez.
* Triez les résultats pour définir l’ordre de l’axe X.

:::image type="content" source="images/samples/color-bar-chart.png" alt-text="Capture d’écran d’un histogramme, avec dix colonnes de couleur décrivant les valeurs respectives de 10 emplacements.":::

## <a name="get-sessions-from-start-and-stop-events"></a>Obtenir des sessions à partir d’événements de démarrage et d’arrêt

Dans un journal d’événements, certains événements marquent le début ou la fin d’une session ou d’une activité étendue. 

|Nom|City|SessionId|Timestamp|
|---|---|---|---|
|Démarrer|London|2817330|2015-12-09T10:12:02.32|
|Jeu|London|2817330|2015-12-09T10:12:52.45|
|Démarrer|Manchester|4267667|2015-12-09T10:14:02.23|
|Arrêter|London|2817330|2015-12-09T10:23:43.18|
|Annuler|Manchester|4267667|2015-12-09T10:27:26.29|
|Arrêter|Manchester|4267667|2015-12-09T10:28:31.72|

Chaque événement possède un ID de session (`SessionId`). La difficulté consiste à faire correspondre les événements de début et de fin avec un ID de session.

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

Pour faire correspondre les événements de début et de fin avec un ID de session :

1. Utilisez [let](./letstatement.md) pour nommer une projection de table réduite au minimum avant d’initier la jointure.
1. Utilisez [project](./projectoperator.md) pour modifier le nom des timestamps afin d’afficher les heures de début et de fin dans les résultats. `project` sélectionne également les autres colonnes à afficher dans les résultats. 
1. Utilisez [join](./joinoperator.md) pour mettre en correspondance les entrées de début et de fin pour une même activité. Une ligne est créée pour chaque activité. 
1. Utilisez à nouveau `project` pour ajouter une colonne et afficher la durée de l’activité.

Voici le format :

|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

## <a name="get-sessions-without-using-a-session-id"></a>Obtenir des sessions sans utiliser d’ID de session

Supposons que les événements de début et de fin n’aient d’ID de session facilitant une mise en correspondance, mais que nous disposions de l’adresse IP du client où la session a eu lieu. En supposant que chaque adresse client effectue une seule session à la fois, nous pouvons établir une correspondance entre chaque événement de début et l’événement de fin suivant à partir de la même adresse IP :

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

La `join` fait correspondre chaque heure de début à toutes les heures de fin à partir de la même adresse IP du client. L’exemple de code :

- Supprime les correspondances comportant des heures de fin antérieures.
- Effectue des regroupements par heure de début et adresse IP pour obtenir un groupe par session. 
- Fournit une fonction `bin` pour le paramètre `StartTime`. Si vous ne suivez pas cette étape, Kusto utilise automatiquement des classes d’une heure correspondant à certaines heures de début avec des heures de fin erronées.

`arg_min` recherche la ligne présentant la plus petite durée dans chaque groupe, et le paramètre `*` est transmis à toutes les autres colonnes. 

L’argument ajoute le préfixe `min_` à chaque nom de colonne. 

:::image type="content" source="images/samples/start-stop-ip-address-table.png" alt-text="Capture d’écran d’une table répertoriant les résultats, avec des colonnes pour l’heure de début, l’adresse IP du client, la durée, la ville et l’arrêt le plus ancien pour chaque combinaison client/heure de début."::: 

Ajoutez du code pour compter les durées dans des classes correctement dimensionnées. Dans cet exemple, en raison d’une préférence pour un graphique à barres, divisez par `1s` pour convertir les intervalles de temps en nombres :

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/number-of-sessions-bar-chart.png" alt-text="Capture d’écran d’un histogramme représentant le nombre de sessions, avec des durées dans les plages spécifiées.":::

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

Supposons que vous disposiez d’une table d’activités et des heures de début et de fin de chacune d’entre elles. Vous pouvez afficher un graphique répertoriant le nombre d’activités exécutées simultanément au fil du temps.

Voici un exemple d’entrée, appelé `X` :

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Pour un graphique des classes d’une minute, vous souhaitez compter chaque activité en cours d’exécution à chaque intervalle d’une minute.

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

Plutôt que de conserver ces tableaux, développez-les à l’aide de [mv-expand](./mvexpandoperator.md) :

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

Regroupez maintenant les résultats par heure d’échantillonnage et comptez les occurrences de chaque activité :

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Utilisez `todatetime()` car [mv-expand](./mvexpandoperator.md) génère une colonne de type dynamique.
* Utilisez `bin()` car, pour les valeurs numériques et les dates, si vous n’indiquez pas d’intervalle, `summarize` applique systématiquement une fonction `bin()` avec un intervalle par défaut. 

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

## <a name="introduce-null-bins-into-summarize"></a>Introduire des classes null dans *summarize*

Lorsque l’opérateur `summarize` est appliqué sur une clé de groupe se composant d’une colonne date/heure, compartimentez ces valeurs dans des classes à largeur fixe :

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

Cet exemple génère une table composée d’une seule ligne par groupe de lignes dans `T` se retrouvant dans chaque classe de cinq minutes.

En revanche, le code n’ajoute pas de classes null - lignes pour les valeurs de classes de temps situées entre `StartTime` et `StopTime` pour lesquelles il n’existe pas de ligne correspondante dans `T`. Il est judicieux de « remplir » la table avec ces classes. Pour ce faire, vous pouvez procéder comme suit :

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

1. Utilisez l’opérateur `union` pour ajouter des lignes à une table. Ces lignes sont générées par l’expression `union`.
1. L’opérateur `range` génère une table comportant une seule ligne et une seule colonne. La table est exclusivement utilisée pour `mv-expand`.
1. L’opérateur `mv-expand` sur la fonction `range` crée autant de lignes qu’il existe de classes de cinq minutes entre `StartTime` et `EndTime`.
1. Utilisez une `Count` sur `0`.
1. L’opérateur `summarize` regroupe les classes de l’argument d’origine (gauche ou externe) vers `union`. L’opérateur compartimente également à partir de l’argument interne (lignes de classe null). Ce processus permet de veiller à ce que la sortie comporte une ligne par classe dont la valeur est égale à zéro ou au nombre d’origine.

## <a name="get-more-from-your-data-by-using-kusto-with-machine-learning"></a>Exploiter au mieux vos données en utilisant Kusto avec le Machine Learning 

De nombreux cas d’usage intéressants font appel à des algorithmes de Machine Learning et dérivent de précieux insights à partir des données de télémétrie. Ces algorithmes requièrent souvent un jeu de données strictement structuré comme entrée. En règle générale, les données de journal brutes ne correspondent ni à la structure ni à la taille requises. 

Commencez par rechercher les anomalies dans le taux d’erreurs d’un service d’inférences Bing spécifique. La table des journaux contient 65 milliards d’enregistrements. La requête de base suivante filtre les 250 000 erreurs, puis crée une série chronologique du nombre d’erreurs utilisant la fonction de détection des anomalies [series_decompose_anomalies](series-decompose-anomaliesfunction.md). Les anomalies sont détectées par le service Kusto et mises en évidence sous forme de points rouges sur le graphique de la série chronologique.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Le service a identifié plusieurs compartiments affichant des taux d’erreurs suspects. Utilisez Kusto pour faire un zoom avant sur ce laps de temps. Exécutez ensuite une requête qui agrège sur la colonne `Message`. Essayez de trouver les erreurs principales. 

Les parties pertinentes de l’ensemble de la trace de pile du message sont découpées pour une meilleure adaptation des résultats à la page. 

Vous pouvez noter que l’identification des huit erreurs principales a abouti. Toutefois, une longue série d’erreurs s’ensuit, car le message d’erreur a été créé à l’aide d’une chaîne de format contenant des données modifiées :

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
|7125|Échec d’appel de InferenceHostService..System.NullReferenceException : Référence d'objet non définie sur une instance d’objet...
|7124|Erreur système d’inférence inattendue..System.NullReferenceException : Référence d'objet non définie sur une instance d’objet... 
|5112|Erreur système d’inférence inattendue..System.NullReferenceException : La référence d’objet n’est pas définie sur une instance d’objet..
|174|Échec d’appel InferenceHostService..System.ServiceModel.CommunicationException: Une erreur d’écriture s’est produite dans le canal :...
|10|Échec de ExecuteAlgorithmMethod pour la méthode « RunCycleFromInterimData »...
|10|Erreur système d’inférence..Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException :...
|3|Échec d’appel InferenceHostService..System.ServiceModel.CommunicationObjectFaultedException :...
|1|Erreur système d’inférence... SocialGraph.BOSS.OperationResponse...AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|Erreur système d’inférence... SocialGraph.BOSS.OperationResponse...AIS TraceId : 5F79F7587FF943EC9B641E02E701AFBF...

À ce stade, l’utilisation de l’opérateur `reduce` peut aider. L’opérateur a identifié 63 erreurs différentes provenant du même point d’instrumentation de trace dans le code. `reduce` permet de se concentrer sur des traces d’erreur plus explicites dans cette fenêtre de temps.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Nombre|Modèle
|---|---
|7125|Échec de ExecuteAlgorithmMethod pour la méthode « RunCycleFromInterimData »...
|  7125|Échec d’appel de InferenceHostService..System.NullReferenceException : Référence d'objet non définie sur une instance d’objet...
|  7124|Erreur système d’inférence inattendue..System.NullReferenceException : Référence d'objet non définie sur une instance d’objet...
|  5112|Erreur système d’inférence inattendue..System.NullReferenceException : Référence d'objet non définie sur une instance d’objet...
|  174|Échec d’appel InferenceHostService..System.ServiceModel.CommunicationException: Une erreur d’écriture s’est produite dans le canal :...
|  63|Erreur système d’inférence..Microsoft.Bing.Platform.Inferences.\* : Write \* pour écrire dans l’objet BOSS.\* : SocialGraph.BOSS.Reques...
|  10|Échec de ExecuteAlgorithmMethod pour la méthode « RunCycleFromInterimData »...
|  10|Erreur système d’inférence..Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException :...
|  3|Échec d’appel InferenceHostService..System.ServiceModel.\*: L’objet, System.ServiceModel.Channels.\*+\*, pour \*\* est \*... pour Syst...

Vous disposez à présent d’un meilleur aperçu des erreurs principales qui ont contribué aux anomalies détectées.

Pour comprendre l’effet de ces erreurs dans l’exemple de système, prenez en compte les points suivants : 
* La table `Logs` contient des données dimensionnelles supplémentaires, telles que `Component` et `Cluster`.
* Le nouveau plug-in autocluster contribue à dériver les détails des composants et des clusters avec une requête simple. 

Dans l’exemple suivant, vous pouvez clairement voir que chacune des quatre erreurs principales relève d’un composant spécifique. En outre, bien que les trois erreurs principales soient spécifiques au cluster DB4, la quatrième erreur se produit sur tous les clusters.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Nombre |Pourcentage (%)|Composant|Cluster|Message
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|ExecuteAlgorithmMethod pour la méthode...
|7125|26.64|Composant inconnu|DB4|Échec d’appel InferenceHostService...
|7124|26.64|InferenceAlgorithmExecutor|DB4|Erreur système d’inférence inattendue...
|5112|19.11|InferenceAlgorithmExecutor|*|Erreur système d’inférence inattendue...

## <a name="map-values-from-one-set-to-another"></a>Mapper des valeurs d’un jeu à un autre

Le mappage statique de valeurs constitue un cas d’usage de requête courant. Le mappage statique permet de mieux présenter les résultats.

Par exemple, dans la table suivante, `DeviceModel` spécifie un modèle d’appareil. L’utilisation du modèle d’appareil ne s’avère pas pratique pour référencer le nom de l’appareil.  

|DeviceModel |Nombre 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

 L’utilisation d’un nom convivial s’avère plus pratique :  

|FriendlyName |Nombre 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone 5 |66 

Les deux exemples suivants montrent comment passer d’un modèle d’appareil à un nom convivial pour identifier un appareil.  

### <a name="map-by-using-a-dynamic-dictionary"></a>Mapper à l’aide d’un dictionnaire dynamique

Vous pouvez obtenir un mappage en utilisant un dictionnaire et des accesseurs dynamiques. Par exemple :

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

|FriendlyName|Nombre|
|---|---|
|iPhone 5|32|
|iPhone 4|432|
|iPhone 6|55|
|iPhone 5|66|

### <a name="map-by-using-a-static-table"></a>Mapper à l’aide d’une table statique

Vous pouvez également obtenir un mappage à l’aide d’une table persistante et d’un opérateur `join`.
 
1. Créez la table de mappage une seule fois :

    ```kusto
    .create table Devices (DeviceModel: string, FriendlyName: string) 

    .ingest inline into table Devices 
        ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
    ```

1. Créez une table du contenu de l’appareil :

    |DeviceModel |FriendlyName 
    |---|---
    |iPhone5,1 |iPhone 5 
    |iPhone3,2 |iPhone 4 
    |iPhone7,2 |iPhone 6 
    |iPhone5,2 |iPhone 5 

1. Créez une source de table de test :

    ```kusto
    .create table Source (DeviceModel: string, Count: int)

    .ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
    ```

1. Joignez les tables et exécutez le projet :

   ```kusto
   Devices  
   | join (Source) on DeviceModel  
   | project FriendlyName, Count
   ```

Voici le format :

|FriendlyName |Nombre 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone 5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Créer et utiliser des tables de dimension de durée de requête

Vous souhaiterez souvent joindre les résultats d’une requête avec une table de dimension ad hoc non stockée dans la base de données. Vous pouvez définir une expression dont le résultat correspond à une table limitée à une seule requête. 

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

Voici un exemple de définition un peu plus complexe :

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

## <a name="retrieve-the-latest-records-by-timestamp-per-identity"></a>Récupérer les derniers enregistrements (par timestamp) par identité

Supposons que vous disposiez d’une table comprenant ce qui suit :
* Une colonne `ID` identifiant l’entité à laquelle chaque ligne est associée, comme un ID d’utilisateur ou un ID de nœud
* Une colonne `timestamp` fournissant la référence temporelle pour la ligne
* D’autres colonnes

Vous pouvez utiliser l’[opérateur top-nested](topnestedoperator.md) pour effectuer une requête qui renvoie les deux derniers enregistrements pour chaque valeur de la colonne `ID`, où _latest_ est défini comme _ayant la valeur la plus élevée de `timestamp`_  :

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


Voici une explication pas à pas de la requête précédente (la numérotation fait référence aux nombres des commentaires de code) :

1. `datatable` permet de générer des données de test à des fins de démonstration. Normalement, vous utilisez des données réelles ici.
1. Cette ligne a vocation à _renvoyer toutes les valeurs distinctes de `id`_ . 
1. Cette ligne renvoie ensuite ce qui suit pour les deux premiers enregistrements qui optimisent :
     * La colonne `timestamp`
     * Les colonnes du niveau précédent (ici, `id`)
     * La colonne spécifiée à ce niveau (ici, `timestamp`)
1. Cette ligne ajoute les valeurs de la colonne `bla` pour chacun des enregistrements renvoyés par le niveau précédent. Si la table contient d’autres colonnes qui vous intéressent, vous pouvez répéter cette ligne pour chacune de ces colonnes.
1. La dernière ligne utilise l’[opérateur project-away](projectawayoperator.md) pour supprimer les colonnes « supplémentaires » introduites par `top-nested`.

## <a name="extend-a-table-by-a-percentage-of-the-total-calculation"></a>Étendre une table à hauteur d’un pourcentage du calcul total

Une expression tabulaire incluant une colonne numérique s’avère plus utile lorsqu’elle est accompagnée de sa valeur sous forme de pourcentage du total.

Par exemple, supposons qu’une requête génère la table suivante :

|SomeSeries|SomeInt|
|----------|-------|
|Apple       |    100|
|Banane       |    200|

Vous souhaitez afficher la table comme suit :

|SomeSeries|SomeInt|Pct |
|----------|-------|----|
|Apple       |    100|33,3|
|Banane       |    200|66,6|

Pour modifier la manière dont la table s’affiche, calculez le total (somme) de la colonne `SomeInt`, puis divisez chaque valeur de cette colonne par le total. Pour obtenir des résultats arbitraires, utilisez l’[opérateur as](asoperator.md).

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

L’exemple suivant montre comment synthétiser des colonnes à l’aide d’une fenêtre glissante. Pour la requête, utilisez la table suivante contenant les prix des fruits par timestamps.

Calculez les coûts minimaux, maximaux et totaux de chaque fruit par jour à l’aide d’une fenêtre glissante de sept jours. Chaque enregistrement du jeu de résultats regroupe les sept jours précédents et les résultats contiennent un enregistrement par jour dans la période d’analyse.

Table des fruits :

|Timestamp|Fruit|Price|
|---|---|---|
|2018-09-24 21:00:00.0000000|Bananes|3|
|2018-09-25 20:00:00.0000000|Pommes|9|
|2018-09-26 03:00:00.0000000|Bananes|4|
|2018-09-27 10:00:00.0000000|Prunes|8|
|2018-09-28 07:00:00.0000000|Bananes|6|
|2018-09-29 21:00:00.0000000|Bananes|8|
|2018-09-30 01:00:00.0000000|Prunes|2|
|2018-10-01 05:00:00.0000000|Bananes|0|
|2018-10-02 02:00:00.0000000|Bananes|0|
|2018-10-03 13:00:00.0000000|Prunes|4|
|2018-10-04 14:00:00.0000000|Pommes|8|
|2018-10-05 05:00:00.0000000|Bananes|2|
|2018-10-06 08:00:00.0000000|Prunes|8|
|2018-10-07 12:00:00.0000000|Bananes|0|

Voici la requête d’agrégation de fenêtre glissante. Consultez l’explication suivant le résultat de la requête.

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
|2018-10-01 00:00:00.0000000|Prunes|2|8|10|
|2018-10-02 00:00:00.0000000|Bananes|0|8|18|
|2018-10-02 00:00:00.0000000|Prunes|2|8|10|
|2018-10-03 00:00:00.0000000|Prunes|2|8|14|
|2018-10-03 00:00:00.0000000|Bananes|0|8|14|
|2018-10-04 00:00:00.0000000|Bananes|0|8|14|
|2018-10-04 00:00:00.0000000|Prunes|2|4|6|
|2018-10-04 00:00:00.0000000|Pommes|8|8|8|
|2018-10-05 00:00:00.0000000|Bananes|0|8|10|
|2018-10-05 00:00:00.0000000|Prunes|2|4|6|
|2018-10-05 00:00:00.0000000|Pommes|8|8|8|
|2018-10-06 00:00:00.0000000|Prunes|2|8|14|
|2018-10-06 00:00:00.0000000|Bananes|0|2|2|
|2018-10-06 00:00:00.0000000|Pommes|8|8|8|
|2018-10-07 00:00:00.0000000|Bananes|0|2|2|
|2018-10-07 00:00:00.0000000|Prunes|4|8|12|
|2018-10-07 00:00:00.0000000|Pommes|8|8|8|

La requête « s’étend » (duplique) chaque enregistrement de la table d’entrée pendant les sept jours suivant son apparition réelle. Chaque enregistrement apparaît en réalité sept fois. Par conséquent, l’agrégation quotidienne comprend tous les enregistrements des sept jours précédents.


Voici une explication pas à pas de la requête précédente : 

1. Compartimentez chaque enregistrement sur un jour (par rapport à `_start`). 
1. Déterminez la fin de la plage par enregistrement : `_bin + 7d`, sauf si la valeur n’est pas comprise dans la plage de `_start` et `_end`, auquel cas elle est ajustée. 
1. Pour chaque enregistrement, créez une table de sept jours (timestamps), en commençant par le jour de l’enregistrement actuel. 
1. `mv-expand` le tableau, en dupliquant chaque enregistrement dans sept enregistrements distants d’une journée. 
1. Exécutez la fonction d’agrégation pour chaque jour. En raison de #4, cette étape synthétise les sept jours _passés_. 
1. Les données des sept premiers jours sont incomplètes en l’absence de période de recherche arrière pour les sept premiers jours. Les sept premiers jours sont exclus du résultat final. Dans l’exemple, ils participent uniquement à l’agrégation pour 2018-10-01.

## <a name="find-the-preceding-event"></a>Rechercher l’événement précédent

L’exemple suivant montre comment rechercher un événement précédent entre deux jeux de données.  

Vous disposez de deux jeux de données, A et B. Pour chaque enregistrement du jeu de données B, recherchez son événement précédent dans le jeu de données A (à savoir, l’enregistrement `arg_max` dans A toujours _antérieur_ à B).

Vous trouverez ci-dessous les exemples de jeux de données : 

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

|Timestamp|ID|EventB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1|
|2019-01-01 00:00:00.0000000|z|Az1|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|o|Ay1|
|2019-01-01 00:00:05.0000000|o|Ay2|

</br>

|Timestamp|ID|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|o|B|
|2019-01-01 00:02:00.0000000|z|B|

Sortie attendue : 

|ID|Timestamp|EventB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|o|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1|

Nous vous recommandons deux approches différentes pour résoudre ce problème. Vous pouvez les tester sur votre jeu de données spécifique afin de trouver celle qui convient le mieux à votre scénario.

> [!NOTE] 
> Chaque approche peut s’exécuter de manière différente sur des jeux de données différents.

### <a name="approach-1"></a>Approche 1

Cette approche sérialise les deux jeux de données par ID et timestamp. Elle regroupe ensuite tous les événements du jeu de données B avec leurs événements précédents dans le jeu de données A. Enfin, elle sélectionne `arg_max` parmi tous les événements du jeu de données A dans le groupe.

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

### <a name="approach-2"></a>Approche 2

Cette approche de résolution du problème requiert une période de recherche arrière maximale. Cette approche examine l’_ancienneté_ de l’enregistrement du jeu de données A par rapport au jeu de données B. Elle joint ensuite les deux jeux de données en fonction de l’ID et de cette période de recherche arrière.

`join` génère tous les candidats possibles, tous les enregistrements du jeu de données A plus anciens que les enregistrements du jeu de données B et ce, au cours de la période de recherche arrière. Ensuite, l’élément le plus proche du jeu de données B est filtré par `arg_min (TimestampB - TimestampA)`. Plus la période de recherche arrière est courte, meilleurs sont les résultats de la requête.

Dans l’exemple suivant, la limite de recherche arrière est définie sur `1m`. L’enregistrement avec l’ID `z` n’a pas d’événement `A` correspondant, car son événement `A` est antérieur de deux minutes.

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

|ID|B_Timestamp|A_Timestamp|EventB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|o|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||


## <a name="next-steps"></a>Étapes suivantes

- Suivez un [tutoriel sur le langage de requête Kusto](tutorial.md?pivots=azuredataexplorer).

::: zone-end

::: zone pivot="azuremonitor"

Cet article identifie les besoins courants en matière de requêtes dans Azure Monitor et explique comment utiliser le langage de requête Kusto afin d’y répondre.

## <a name="string-operations"></a>Opérations de chaîne

Les sections suivantes fournissent des exemples montrant comment utiliser les chaînes avec le langage de requête Kusto.

### <a name="strings-and-how-to-escape-them"></a>Chaînes et échappement

Les valeurs de chaîne sont entourées de guillemets simples ou doubles. Ajoutez la barre oblique inverse (\\) à gauche d’un caractère pour placer le caractère dans une séquence d’échappement : `\t` pour tabulation, `\n` pour saut de ligne et `\"` pour un guillemet simple.

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
`has`          |La valeur de droite correspond à un terme entier dans la valeur de gauche |Non|`"North America" has "america"`
`!has`         |La valeur de droite ne correspond pas à un terme entier dans la valeur de gauche       |Non            |`"North America" !has "amer"` 
`has_cs`       |La valeur de droite correspond à un terme entier dans la valeur de gauche |Oui|`"North America" has_cs "America"`
`!has_cs`      |La valeur de droite ne correspond pas à un terme entier dans la valeur de gauche       |Oui            |`"North America" !has_cs "amer"` 
`hasprefix`    |La valeur de droite correspond à un préfixe de terme dans la valeur de gauche         |Non            |`"North America" hasprefix "ame"`
`!hasprefix`   |La valeur de droite ne correspond pas à un préfixe de terme dans la valeur de gauche     |Non            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |La valeur de droite correspond à un préfixe de terme dans la valeur de gauche         |Oui            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |La valeur de droite ne correspond pas à un préfixe de terme dans la valeur de gauche     |Oui            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |La valeur de droite correspond à un suffixe de terme dans la valeur de gauche         |Non            |`"North America" hassuffix "ica"`
`!hassuffix`   |La valeur de droite ne correspond pas à un suffixe de terme dans la valeur de gauche     |Non            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |La valeur de droite correspond à un suffixe de terme dans la valeur de gauche         |Oui            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |La valeur de droite ne correspond pas à un suffixe de terme dans la valeur de gauche     |Oui            |`"North America" !hassuffix_cs "icA"`
`contains`     |La valeur de droite correspond à une sous-séquence de la valeur de gauche  |Non            |`"FabriKam" contains "BRik"`
`!contains`    |La valeur de droite n’intervient pas dans la valeur de gauche           |Non            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |La valeur de droite correspond à une sous-séquence de la valeur de gauche  |Oui           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |La valeur de droite n’intervient pas dans la valeur de gauche           |Oui           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |La valeur de droite correspond à une sous-séquence initiale de la valeur de gauche|Non            |`"Fabrikam" startswith "fab"`
`!startswith`  |La valeur de droite ne correspond pas à une sous-séquence initiale de la valeur de gauche|Non        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |La valeur de droite correspond à une sous-séquence initiale de la valeur de gauche|Oui            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |La valeur de droite ne correspond pas à une sous-séquence initiale de la valeur de gauche|Oui        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |La valeur de droite correspond à une sous-séquence fermante de la valeur de gauche|Non             |`"Fabrikam" endswith "Kam"`
`!endswith`    |La valeur de droite ne correspond pas à une sous-séquence fermante de la valeur de gauche|Non         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |La valeur de droite correspond à une sous-séquence fermante de la valeur de gauche|Oui             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |La valeur de droite ne correspond pas à une sous-séquence fermante de la valeur de gauche|Oui         |`"Fabrikam" !endswith "brik"`
`matches regex`|La valeur de gauche contient une correspondance pour la valeur de droite|Oui           |`"Fabrikam" matches regex "b.*k"`
`in`           |Égal à l’un des éléments       |Oui           |`"abc" in ("123", "345", "abc")`
`!in`          |N’est égal à aucun des éléments   |Oui           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>*countof*

Compte les occurrences d’une sous-chaîne dans une chaîne. Peut correspondre à des chaînes simples ou utiliser une expression régulière (regex). Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

```
countof(text, search [, kind])
```

- `text` : Chaîne d’entrée 
- `search` : Chaîne simple ou expression régulière à faire correspondre au texte
- `kind` : _normal_ | _regex_ (par défaut : normal).

Renvoie le nombre de fois où la chaîne de recherche peut être mise en correspondance dans le conteneur. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

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

Obtient une correspondance pour une expression régulière à partir d’une chaîne spécifique. Peut également convertir la sous-chaîne extraite selon le type indiqué.

```kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex` : Expression régulière.
- `captureGroup` : Constante entière positive indiquant le groupe de capture à extraire. Utilisez 0 pour la correspondance entière, 1 pour la valeur mise en correspondance par la première parenthèse \(\) dans l’expression régulière et 2 ou plus pour les parenthèses suivantes.
- `text` - Chaîne à rechercher.
- `typeLiteral` - Littéral de type facultatif (par exemple, `typeof(long)`). Si elle est fournie, la sous-chaîne extraite est convertie dans ce type.

Renvoie la sous-chaîne correspondant au groupe de capture indiqué `captureGroup`, éventuellement convertie en `typeLiteral`. Si aucune correspondance n’est trouvée ou si la conversion de type échoue, renvoie la valeur null.

L’exemple suivant extrait le dernier octet de `ComputerIP` à partir d’un enregistrement de pulsation :

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

L’exemple suivant extrait le dernier octet, le caste en type *real* (nombre), puis calcule la valeur d’adresse IP suivante :

```kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Dans l’exemple suivant, une définition de `Duration` est recherchée dans la chaîne `Trace`. La correspondance est castée en `real` et multipliée par une constante de temps (1 s) qui caste `Duration` en type `timespan`.

```kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>*isempty*, *isnotempty*, *notempty*

- `isempty` renvoie `true` si l’argument est une chaîne vide ou null (voir `isnull`).
- `isnotempty` renvoie `true` si l’argument n’est pas une chaîne vide ou null (voir `isnotnull`). Alias : `notempty`.


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


### <a name="parseurl"></a>*parseurl*

Fractionne une URL en plusieurs parties, comme protocole, hôte et port, puis renvoie un objet dictionnaire contenant les parties en tant que chaînes.

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

- `regex` : Expression régulière à mettre en correspondance. Elle peut contenir des groupes de capture entre parenthèses \(\).
- `rewrite` : Expression régulière de remplacement pour toute correspondance trouvée par une expression régulière correspondante. Utilisez \0 pour faire référence à la correspondance complète, \1 pour le premier groupe de capture, \2 et ainsi de suite pour les groupes de capture suivants.
- `input_text` : Chaîne d’entrée dans laquelle effectuer une recherche.

Renvoie le texte après le remplacement de toutes les correspondances de l’expression régulière par les évaluations de réécriture. Les correspondances ne se chevauchent pas.

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

Fractionne une chaîne spécifique en fonction d’un délimiteur spécifié, puis renvoie un tableau des sous-chaînes obtenues.

```
split(source, delimiter [, requestedIndex])
```

- `source` : Chaîne à fractionner en fonction du délimiteur spécifié.
- `delimiter` : Délimiteur utilisé pour fractionner la chaîne source.
- `requestedIndex` : Index de base zéro facultatif. S’il est fourni, le tableau de chaînes renvoyé contient uniquement cet élément (s’il existe).


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

Extrait une sous-chaîne d’une chaîne source spécifique à partir de l’index indiqué. Vous pouvez spécifier la longueur de la sous-chaîne demandée.

```
substring(source, startingIndex [, length])
```

- `source` : Chaîne source dont la sous-chaîne est extraite.
- `startingIndex` : Position du caractère de départ (base zéro) de la sous-chaîne demandée.
- `length` : Paramètre facultatif permettant de spécifier la longueur demandée de la sous-chaîne renvoyée.

#### <a name="example"></a>Exemple

```kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>*tolower*, *toupper*

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

Les sections suivantes fournissent des exemples montrant comment utiliser les valeurs de date et d’heure avec le langage de requête Kusto.

### <a name="date-time-basics"></a>Notions de base de date et d’heure

Le langage de requête Kusto comporte deux principaux types de données associés aux dates et heures : `datetime` et `timespan`. Toutes les dates sont exprimées au format UTC. Même si plusieurs formats date/heure sont pris en charge, le format ISO-8601 est préféré. 

Les types timespan sont exprimés en tant que valeur décimale suivie d’une unité de temps :

|Raccourci   | Unité de temps    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | milliseconde  |
|microseconde | microseconde  |
|graduation        | nanoseconde   |

Vous pouvez créer des valeurs date/heure en castant une chaîne à l’aide de l’opérateur `todatetime`. Par exemple, pour passer en revue les pulsations de machine virtuelle envoyées dans un laps de temps spécifique, utilisez l’opérateur `between` afin de spécifier une plage de temps :

```kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Un autre scénario courant consiste à comparer une valeur date/heure au présent. Par exemple, pour afficher toutes les pulsations au cours des deux dernières minutes, vous pouvez utiliser l’opérateur `now` avec une valeur timespan qui représente deux minutes :

```kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Un raccourci est également disponible pour cette fonction :

```kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

La méthode la plus rapide et la plus lisible consiste à utiliser l’opérateur `ago` :

```kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Supposons qu’au lieu de connaître l’heure de début et de fin, vous connaissiez l’heure de début et la durée. Vous pouvez réécrire la requête :

```kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="convert-time-units"></a>Convertir les unités de temps

Il peut être utile d’exprimer une valeur de date/heure ou d’intervalle de temps dans une unité de temps autre que celle par défaut. Par exemple, si vous examinez les événements d’erreur des 30 dernières minutes et avez besoin d’une colonne calculée qui affiche le temps écoulé depuis que l’événement s’est produit, vous pouvez utiliser cette requête :

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

La colonne `timeAgo` contient des valeurs comme `00:09:31.5118992`, qui se présentent au format hh:mm:ss.fffffff. Si vous souhaitez mettre en forme ces valeurs selon le `number` de minutes depuis l’heure de début, divisez cette valeur par `1m` :

```kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```

### <a name="aggregations-and-bucketing-by-time-intervals"></a>Agrégations et création de compartiments par intervalles de temps

Un autre scénario courant se caractérise par la nécessité d’obtenir des statistiques pour une période de temps spécifique dans une unité de temps spécifique. Pour ce scénario, vous pouvez utiliser un opérateur `bin` dans le cadre d’une clause `summarize`.

Utilisez la requête suivante pour obtenir le nombre d’événements qui se sont produits toutes les cinq minutes au cours de la dernière demi-heure :

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

Une autre façon de créer des compartiments de résultats consiste à utiliser des fonctions telles que `startofday` :

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

Toutes les valeurs date/heure étant exprimées au format UTC, il est souvent utile de convertir ces valeurs dans le fuseau horaire local. Par exemple, utilisez ce calcul pour convertir les heures UTC en PST :

```kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>Agrégations

Les sections suivantes fournissent des exemples montrant comment agréger les résultats d’une requête avec le langage de requête Kusto.

### <a name="count"></a>*count*

Comptez le nombre de lignes du jeu de résultats une fois que tous les filtres sont appliqués. L’exemple suivant retourne le nombre total de lignes dans la table `Perf` au cours des 30 dernières minutes. Les résultats sont renvoyés dans une colonne nommée `count_`, sauf si vous lui attribuez un nom spécifique :


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

Une visualisation de graphique temporel peut être utile pour voir une tendance au fil du temps :

```kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

La sortie de cet exemple affiche la courbe de tendance du nombre d’enregistrements `Perf` dans des intervalles de cinq minutes :


:::image type="content" source="images/samples/perf-count-line-chart.png" alt-text="Capture d’écran d’un graphique en courbes affichant la courbe de tendance du nombre d’enregistrements Perf dans des intervalles de cinq minutes.":::

### <a name="dcount-dcountif"></a>*dcount*, *dcountif*

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

Pour effectuer un compte ou d’autres agrégations de sous-groupes dans vos données, utilisez le mot clé `by`. Par exemple, pour compter le nombre d’ordinateurs Linux distincts qui ont envoyé des pulsations dans chaque pays ou région, utilisez cette requête :

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


Pour analyser des sous-groupes encore plus petits de vos données, ajoutez des noms de colonnes à la section `by`. Par exemple, vous pouvez peut-être compter le nombre d’ordinateurs distincts de chaque pays ou région par type de système d’exploitation (`OSType`) :

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

Vous pouvez également spécifier différents centiles afin d’obtenir un résultat agrégé pour chacun :

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Les résultats peuvent indiquer que certains processeurs de l’ordinateur présentent des valeurs médianes similaires. Toutefois, si certains ordinateurs sont stables autour de la valeur médiane, d’autres ont signalé des valeurs de processeur plus faibles et plus élevées. Les valeurs élevées et faibles indiquent que les ordinateurs ont enregistré des pics.

### <a name="variance"></a>Variance

Pour évaluer directement la variance d’une valeur, utilisez les méthodes de variance et d’écart type :

```kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Un bon moyen d’analyser la stabilité de l’utilisation de l’UC consiste à combiner `stdev` avec le calcul de la valeur médiane :

```kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generate-lists-and-sets"></a>Générer des listes et des ensembles

Vous pouvez utiliser `makelist` pour déplacer des données selon l’ordre des valeurs dans une colonne spécifique. Par exemple, vous voulez explorer l’ordre le plus courant dans lequel les événements se produisent sur vos ordinateurs. Vous pouvez avant tout déplacer les données selon l’ordre des valeurs `EventID` sur chaque ordinateur : 

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

`makelist` génère une liste dans l’ordre dans lequel les données lui ont été passées. Pour trier les événements du plus ancien au plus récent, utilisez `asc` dans l’instruction `order` plutôt que `desc`. 

Il peut s’avérer utile de créer une liste de valeurs distinctes uniquement. Cette liste est appelée _jeu_, et vous pouvez la générer à l’aide de la commande `makeset` :

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

Comme `makelist`, `makeset` fonctionne avec des données ordonnées. La commande `makeset` génère des tableaux en fonction de l’ordre des lignes qui lui sont transmises.

### <a name="expand-lists"></a>Développer les listes

L’opération inverse de `makelist` ou `makeset` est `mv-expand`. La commande `mv-expand` développe une liste de valeurs pour séparer les lignes. L’extension peut porter sur n’importe quel nombre de colonnes dynamiques, y compris des colonnes JSON et de tableau. Par exemple, vous pouvez rechercher dans la table `Heartbeat` les solutions qui ont envoyé des données à partir d’ordinateurs qui ont envoyé une pulsation dans la dernière heure :

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


Vous pouvez utiliser `makelist` pour regrouper des éléments. Dans la sortie, vous pouvez voir la liste d’ordinateurs par solution :

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

### <a name="missing-bins"></a>Classes manquantes

Une application utile de `mv-expand` consiste à renseigner les valeurs par défaut des classes manquantes. Par exemple, supposons que vous recherchiez la durée de bon fonctionnement d’un ordinateur spécifique en explorant sa pulsation. Vous souhaitez également voir la source de la pulsation, qui se trouve dans la colonne `Category`. En règle générale, nous utilisons une instruction `summarize` de base :

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

Dans la sortie, le compartiment associé à « 2017-06-06T19:00:00Z » est manquant, car il n’existe pas de données de pulsation pour cette heure. Utilisez la fonction `make-series` pour affecter une valeur par défaut aux compartiments vides. Une ligne est générée pour chaque catégorie. La sortie inclut deux colonnes de tableau supplémentaires, une pour les valeurs et une pour les intervalles de temps correspondants :

```kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

Voici le format :

| Category | count_ | TimeGenerated |
|---|---|---|
| Agent direct | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Le troisième élément du tableau *count_* est 0, comme prévu. Le tableau _TimeGenerated_ présente un timestamp correspondant « 2017-06-06T19:00:00.0000000 Z ». Ce format de tableau s’avère cependant difficile à lire. Utilisez `mv-expand` pour étendre les tableaux et produire le même format de sortie que celui généré par `summarize` :

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



### <a name="narrow-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Limiter les résultats à un ensemble d’éléments : *let*, *makeset*, *toscalar*, *in*

Un scénario courant consiste à sélectionner les noms d’entités spécifiques selon un ensemble de critères, puis à filtrer un autre jeu de données selon cet ensemble d’entités. Par exemple, vous pouvez rechercher les ordinateurs qui sont connus comme ayant des mises à jour manquantes et identifier les adresses IP auxquelles ces ordinateurs font appel.

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

Vous pouvez utiliser des jointures pour analyser les données provenant de plusieurs tables dans la même requête. Une jointure fusionne les lignes de deux jeux de données en faisant correspondre les valeurs des colonnes spécifiées.

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

Dans l’exemple, le premier jeu de données filtre tous les événements de connexion. Ce jeu de données est joint au premier, qui filtre tous les événements de déconnexion. Les colonnes projetées sont `Computer`, `Account`, `TargetLogonId` et `TimeGenerated`. Les jeux de données sont corrélés par une colonne partagée, `TargetLogonId`. La sortie correspond à un seul enregistrement par corrélation, avec à la fois l’heure de connexion et de déconnexion.

Si les deux jeux de données présentent des colonnes portant le même nom, un numéro d’index est attribué aux colonnes du jeu de données de droite. Dans cet exemple, les résultats affichent `TargetLogonId` avec les valeurs de la table de gauche et `TargetLogonId1` avec les valeurs de la table de droite. Dans ce cas, la deuxième colonne `TargetLogonId1` a été supprimée à l’aide de l’opérateur `project-away`.

> [!NOTE]
> Pour améliorer les performances, conservez uniquement les colonnes appropriées des jeux de données joints à l’aide de l’opérateur `project`.


Utilisez la syntaxe suivante pour joindre deux jeux de données dans lesquels la clé jointe porte un nom différent entre les deux tables :

```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>Tables de recherche

Une utilisation courante des jointures consiste à recourir à `datatable` pour le mappage des valeurs statiques. L’utilisation de `datatable` améliore la présentation des résultats. Par exemple, vous pouvez enrichir les données d’événements de sécurité avec le nom de l’événement pour chaque ID d’événement :

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
| Un descripteur d’objet a été fermé | 290,995 |
| Un descripteur d’objet a été demandé | 154,157 |
| Un descripteur d’objet a fait l’objet d’une tentative de duplication | 144,305 |
| Une tentative d’accès à un objet a eu lieu | 123,669 |
| Opération de chiffrement | 153,495 |
| Opération de fichier de clé | 153,496 |

## <a name="json-and-data-structures"></a>JSON et les structures de données

Les objets imbriqués sont des objets qui contiennent d’autres objets dans un tableau ou un mappage de paires clé-valeur. Ces objets sont représentés sous forme de chaînes JSON. Cette section explique comment utiliser JSON pour récupérer des données et analyser les objets imbriqués.

### <a name="work-with-json-strings"></a>Utiliser des chaînes JSON

Utilisez `extractjson` pour accéder à un élément JSON spécifique dans un chemin connu. Cette fonction nécessite une expression de chemin qui utilise les conventions suivantes :

- Utilisez _$_ pour faire référence au dossier racine.
- Utilisez la notation entre crochets ou sous forme de points pour faire référence aux index et aux éléments, comme illustré dans les exemples suivants.


Utilisez des crochets pour les index et des points pour séparer les éléments :

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Cet exemple est similaire, mais il utilise uniquement la notation entre crochets :

```kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Pour un seul élément, vous pouvez uniquement utiliser la notation sous forme de points :

```kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>*parsejson*

Il est plus facile d’accéder à plusieurs éléments de votre structure JSON en tant qu’objet dynamique. Utilisez `parsejson` pour caster des données de texte en objet dynamique. Après avoir converti le code JSON en type dynamique, vous pouvez utiliser des fonctions supplémentaires pour analyser les données.

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

Utilisez `mv-expand` pour répartir les propriétés d’un objet dans des lignes séparées :

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

Le résultat correspond à un schéma au format JSON :

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

Le schéma décrit les noms des champs d’objets et leurs types de données correspondants. 

Les objets imbriqués peuvent avoir des schémas différents, comme dans l’exemple suivant :

```kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>Graphiques

Les sections suivantes fournissent des exemples montrant comment utiliser les graphiques avec le langage de requête Kusto.

### <a name="chart-the-results"></a>Présenter les résultats sous forme de graphique

Commencez par examiner le nombre d’ordinateurs par système d’exploitation au cours de la dernière heure :

```kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Par défaut, les résultats s’affichent sous forme de table :

:::image type="content" source="images/samples/query-results-table.png" alt-text="Capture d’écran montrant les résultats de la requête dans une table.":::

Pour un meilleur affichage, sélectionnez **Graphique**, puis l’option **Secteurs** afin de visualiser les résultats :

:::image type="content" source="images/samples/query-results-pie-chart.png" alt-text="Capture d’écran montrant les résultats de la requête dans un graphique à secteurs.":::

### <a name="timecharts"></a>Graphiques temporels

Affichez la moyenne, le 50e et le 95e centiles de temps processeur dans des classes d’une heure. 

La requête suivante génère plusieurs séries. Dans les résultats, vous pouvez choisir la série à afficher dans le graphique temporel.

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Sélectionnez l’option d’affichage de graphique **Courbes** :

:::image type="content" source="images/samples/multiple-series-line-chart.png" alt-text="Capture d’écran montrant un graphique en courbes à plusieurs séries.":::

#### <a name="reference-line"></a>Ligne de référence

Une ligne de référence peut vous aider à identifier facilement si la métrique a dépassé un seuil spécifique. Pour ajouter une ligne à un graphique, étendez le jeu de données en ajoutant une colonne constante :

```kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

:::image type="content" source="images/samples/multiple-series-threshold-line-chart.png" alt-text="Capture d’écran montrant un graphique en courbes à plusieurs séries avec une ligne de référence de seuil.":::


### <a name="multiple-dimensions"></a>Plusieurs dimensions

Plusieurs expressions de la clause `by` de `summarize` créent plusieurs lignes dans les résultats. Une ligne est créée pour chaque combinaison de valeurs.

```kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Lorsque vous affichez les résultats sous forme de graphique, celui-ci utilise la première colonne de la clause `by`. L’exemple suivant montre un histogramme empilé créé avec la valeur `EventID`. Les dimensions doivent être de type `string`. Dans cet exemple, la valeur `EventID` est castée sur `string` :

:::image type="content" source="images/samples/select-column-chart-type-eventid.png" alt-text="Capture d’écran montrant un graphique à barres basé sur la colonne EventID.":::

Vous pouvez changer de colonne en sélectionnant la flèche déroulante du nom de la colonne souhaitée :

:::image type="content" source="images/samples/select-column-chart-type-accounttype.png" alt-text="Capture d’écran montrant un graphique à barres basé sur la colonne AccountType, avec le sélecteur de colonne visible.":::

## <a name="smart-analytics"></a>Analytique intelligente

Cette section contient des exemples qui utilisent des fonctions d’analytique intelligente dans Azure Log Analytics pour analyser l’activité des utilisateurs. Vous pouvez utiliser ces exemples pour analyser vos propres applications supervisées par Azure Application Insights, ou utiliser les concepts de ces requêtes pour effectuer une analyse similaire sur d’autres données. 

### <a name="cohorts-analytics"></a>Analytique des cohortes

L’analyse des cohortes effectue le suivi de l’activité de groupes d’utilisateurs spécifiques, appelés _cohortes_. L’analyse des cohortes tente de mesurer le degré d’attractivité d’un service en mesurant le taux de retour des utilisateurs. Les utilisateurs sont regroupés d’après le moment où ils ont utilisé le service pour la première fois. Lors de l’analyse des cohortes, nous nous attendons à observer une baisse d’activité sur les premières périodes suivies. Le titre de chaque cohorte correspond à la semaine durant laquelle ses membres ont été observés pour la première fois.

L’exemple suivant analyse le nombre d’activités que les utilisateurs ont effectuées durant les cinq semaines suivant leur première utilisation du service :

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

L’exemple suivant utilise l’analyse des séries chronologiques avec la fonction [series_fir](/azure/kusto/query/series-firfunction). Vous pouvez utiliser la fonction `series_fir` pour les calculs de fenêtre glissante. L’exemple d’application supervisée est un magasin en ligne qui assure le suivi de l’activité des utilisateurs par le biais d’événements personnalisés. La requête suit deux types d’activités utilisateur : `AddToCart` et `Checkout`. Elle définit un utilisateur actif en tant qu’utilisateur ayant effectué une extraction au moins une fois un jour spécifique.

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

:::image type="content" source="images/samples/rolling-monthly-active-users-chart.png" alt-text="Capture d’écran d’un graphique montrant le cumul d’utilisateurs actifs par jour sur un mois.":::

L’exemple suivant convertit la requête précédente en fonction réutilisable. L’exemple utilise ensuite la requête pour calculer le cumul d’adhérence utilisateur. Un utilisateur actif de cette requête est défini en tant qu’utilisateur ayant effectué une extraction au moins une fois un jour spécifique.

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

:::image type="content" source="images/samples/user-stickiness-chart.png" alt-text="Capture d’écran d’un graphique montrant l’adhérence utilisateur au fil du temps.":::

### <a name="regression-analysis"></a>Analyse de régression

Cet exemple montre comment créer un détecteur automatisé pour les interruptions de service basé exclusivement sur les journaux d’activité des traces d’une application. Le détecteur recherche les augmentations soudaines et anormales dans la quantité relative de traces d’erreur et d’avertissement dans l’application.

Deux techniques sont utilisées pour évaluer l’état du service en fonction des données de journaux d’activité des traces :

- Utilisez [make-series](/azure/kusto/query/make-seriesoperator) pour convertir les journaux d’activité des traces textuels semi-structurés en une métrique qui représente le rapport entre les lignes de traces positives et négatives.
- Utilisez [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) et [series_fit_line](/azure/kusto/query/series-fit-linefunction) pour effectuer une détection de saut avancée à l’aide d’une analyse des séries chronologiques avec une régression linéaire de deux lignes.

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

- Suivez un [tutoriel sur le langage de requête Kusto](tutorial.md?pivots=azuremonitor).


::: zone-end

