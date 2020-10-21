---
title: Exemples-Azure Explorateur de données
description: Cet article décrit des exemples dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0db8c472ed3b23a1bf46f8fce9cbd38b0ca960b3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251031"
---
# <a name="samples"></a>Exemples

Voici quelques-uns des besoins les plus courants en matière de requête et comment le langage de requête Kusto peut être utilisé pour les satisfaire.

## <a name="display-a-column-chart"></a>Afficher un histogramme

Projetez au moins deux colonnes et utilisez-les comme axe des x et y d’un graphique.

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* La première colonne forme l’axe des x. Il peut s’agir d’une valeur numérique, DateTime ou String. 
* Utilisez `where` , `summarize` et `top` pour limiter le volume de données que vous affichez.
* Triez les résultats pour définir l’ordre de l’axe x.

:::image type="content" source="images/samples/060.png" alt-text="Capture d’écran d’un histogramme. L’axe des y est compris entre 0 et environ 50. Dix colonnes colorées représentent les valeurs respectives de 10 emplacements.":::

## <a name="get-sessions-from-start-and-stop-events"></a>Obtenir des sessions à partir d’événements de démarrage et d’arrêt

Supposons que vous avez un journal d’événements. Certains événements marquent le début ou la fin d’une activité ou d’une session étendue. 

|Nom|City|SessionId|Timestamp|
|---|---|---|---|
|Démarrer|London|2817330|2015-12-09T10:12:02.32|
|Jeu|London|2817330|2015-12-09T10:12:52.45|
|Démarrer|Manchester|4267667|2015-12-09T10:14:02.23|
|Arrêter|London|2817330|2015-12-09T10:23:43.18|
|Annuler|Manchester|4267667|2015-12-09T10:27:26.29|
|Arrêter|Manchester|4267667|2015-12-09T10:28:31.72|

Chaque événement a un SessionId. Le problème consiste à mettre en correspondance les événements de démarrage et d’arrêt ayant le même ID.

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

1. Utilisez [`let`](./letstatement.md) pour nommer une projection de la table qui est réduite le plus possible avant de passer à la jointure.
1. Utilisez [`Project`](./projectoperator.md) pour modifier les noms des horodateurs afin que les heures de début et de fin puissent apparaître dans le résultat. 
   Il sélectionne également les autres colonnes à afficher dans le résultat. 
1. Utilisez [`join`](./joinoperator.md) pour mettre en correspondance les entrées de démarrage et d’arrêt de la même activité, en créant une ligne pour chaque activité. 
1. Enfin, `project` ajoute de nouveau une colonne pour afficher la durée de l’activité.


|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>Recevoir des sessions, sans ID de session

Supposons que les événements de démarrage et d’arrêt ne disposent pas d’un ID de session avec lequel nous pouvons faire correspondre. Toutefois, nous avons une adresse IP du client où la session a eu lieu. En supposant que chaque adresse client effectue une seule session à la fois, nous pouvons établir une correspondance entre chaque événement de démarrage et l’événement d’arrêt suivant à partir de la même adresse IP.

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

La jointure fait correspondre chaque heure de démarrage à toutes les heures d’arrêt à partir de la même adresse IP du client. 
1. Supprimer les correspondances avec des temps d’arrêt antérieurs.
1. Regroupez par heure de début et adresse IP pour obtenir un groupe pour chaque session. 
1. Fournissez une `bin` fonction pour le paramètre startTime. Si vous ne le faites pas, Kusto utilisera automatiquement des emplacements de 1 heure qui correspondront à des heures de démarrage avec des heures d’arrêt incorrectes.

`arg_min` sélectionne la ligne avec la plus petite durée dans chaque groupe, et le `*` paramètre passe par toutes les autres colonnes. L’argument préfixe « min_ » à chaque nom de colonne. 

:::image type="content" source="images/samples/040.png" alt-text="Capture d’écran d’un histogramme. L’axe des y est compris entre 0 et environ 50. Dix colonnes colorées représentent les valeurs respectives de 10 emplacements."::: 

Ajoutez du code pour compter les durées dans des emplacements facilement dimensionnés. Dans cet exemple, en raison d’une préférence pour un graphique à barres, divisez par `1s` pour convertir les intervalles en nombres. 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="Capture d’écran d’un histogramme. L’axe des y est compris entre 0 et environ 50. Dix colonnes colorées représentent les valeurs respectives de 10 emplacements.":::

### <a name="real-example"></a>Exemple concret

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

Supposons que vous avez une table d’activités avec les heures de début et de fin. Affichez un graphique dans le temps qui indique combien d’activités sont exécutées simultanément à tout moment.

Voici un exemple d’entrée, appelé `X` .

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Pour un graphique dans des emplacements de 1 minute, créez un texte qui, à chaque million de dollars, contient un nombre pour chaque activité en cours d’exécution.

Voici un résultat intermédiaire.

```kusto
X | extend samples = range(bin(StartTime, 1m), StopTime, 1m)
```

`range` génère un tableau de valeurs aux intervalles spécifiés.

|SessionId | StartTime | StopTime  | exemples|
|---|---|---|---|
| a | 10:01:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

Au lieu de conserver ces tableaux, développez-les à l’aide de [MV-Expand](./mvexpandoperator.md).

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

À présent, regroupez-les par durée d’échantillonnage, en comptant les occurrences de chaque activité.

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Utilisez ToDateTime (), car [MV-Expand](./mvexpandoperator.md) produit une colonne de type dynamique.
* Utilisez bin () car, pour les valeurs numériques et les dates, la synthèse applique toujours une fonction bin avec un intervalle par défaut si vous n’en fournissez pas une. 


| count_SessionId | exemples|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Les résultats peuvent être rendus sous la forme d’un graphique à barres ou d’un graphique temporel.

## <a name="introduce-null-bins-into-summarize"></a>Introduire des emplacements null dans le résumé

Lorsque l' `summarize` opérateur est appliqué sur une clé de groupe qui se compose d’une `datetime` colonne, « bin » ces valeurs en emplacements à largeur fixe.

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

L’exemple ci-dessus produit une table avec une seule ligne par groupe de lignes dans `T` chaque emplacement de cinq minutes. Ce qu’il ne fait pas est d’ajouter des « emplacements null »--lignes pour les valeurs de l’intervalle de temps entre `StartTime` et `StopTime` pour lesquelles il n’y a aucune ligne correspondante dans `T` . 

Il est souhaitable de « remplir » la table avec ces emplacements. Voici une façon d’y parvenir.

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

Voici une explication pas à pas de la requête ci-dessus : 

1. L' `union` opérateur vous permet d’ajouter des lignes à une table. Ces lignes sont produites par l' `union` expression.
1. L' `range` opérateur produit une table avec une seule ligne ou colonne.
   La table n’est pas utilisée pour les opérations autres que pour `mv-expand` .
1. L' `mv-expand` opérateur sur la `range` fonction crée autant de lignes qu’il y a de casiers de 5 minutes entre `StartTime` et `EndTime` .
1. Utilisez un `Count` de `0` .
1. L' `summarize` opérateur groupe les emplacements de l’argument d’origine (gauche ou externe) à `union` . L’opérateur est également un emplacement de l’argument interne (lignes bin null). Ce processus garantit que la sortie comporte une ligne par emplacement, dont la valeur est égale à zéro ou au nombre d’origine.  

## <a name="get-more-out-of-your-data-in-kusto-with-machine-learning"></a>Tirez le meilleur parti de vos données dans Kusto avec Machine Learning 

Il existe de nombreux cas d’usage intéressants qui tirent parti des algorithmes de Machine Learning et dérivent des Insights intéressants des données de télémétrie. Souvent, ces algorithmes nécessitent un jeu de données très structuré comme entrée. En règle générale, les données brutes du journal ne correspondent pas à la structure et à la taille requises. 

Commencez par Rechercher les anomalies dans le taux d’erreur d’un service d’inférences Bing spécifique. La table logs contient des enregistrements 65B. La requête simple ci-dessous filtre les erreurs 250 000 et crée un nombre d’erreurs de série chronologique qui utilise la fonction de détection d’anomalie [series_decompose_anomalies](series-decompose-anomaliesfunction.md). Les anomalies sont détectées par le service Kusto et sont mises en surbrillance en tant que points rouges sur le graphique de série chronologique.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Le service a identifié des intervalles de temps avec un taux d’erreur suspect. Utilisez Kusto pour effectuer un zoom avant sur ce laps de temps, puis exécutez une requête qui effectue un agrégat sur la colonne « message ». Essayez de trouver les erreurs principales. 

Les parties pertinentes de l’intégralité de la trace de la pile du message sont découpées pour mieux tenir sur la page. 

Vous pouvez voir la réussite de l’identification des huit principales erreurs. Toutefois, il suit une longue série d’erreurs, puisque le message d’erreur a été créé par une chaîne de format qui contenait des données modifiées. 

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

C’est là que l' `reduce` opérateur vous aide. L’opérateur a identifié 63 Erreurs différentes qui proviennent du même point d’instrumentation de trace dans le code et permet de se concentrer sur des traces d’erreurs plus explicites dans cette fenêtre de temps.

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
|  7125|Appel InferenceHostService failed..SysTEM. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet...
|  7124|Système inférence inattendue error..SysTEM. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet...
|  5112|Système inférence inattendue error..SysTEM. NullReferenceException : la référence d’objet n’est pas définie sur une instance d’un objet.
|  174|InferenceHostService appelez failed..SysTEM. ServiceModel. CommunicationException : une erreur s’est produite lors de l’écriture dans le canal :...
|  63|Erreur système d’inférence.. Microsoft. Bing. Platform. inférences. \* : écrire \* pour écrire dans le patron de l’objet. \* : SocialGraph. patron. Request...
|  10|Échec de ExecuteAlgorithmMethod pour la méthode « RunCycleFromInterimData »...
|  10|Erreur système d’inférence.. Microsoft. Bing. Platform. inférences. service. managers. UserInterimDataManagerException :...
|  3|Appel InferenceHostService failed..SysTEM. ServiceModel. \* : l’objet, System. ServiceModel. Channels. \* + \* , pour \* \* est le \* ...   à edémarrage...

Vous avez maintenant une bonne vue des erreurs principales qui ont contribué aux anomalies détectées.

Pour comprendre l’impact de ces erreurs sur l’exemple de système : 
* La table « logs » contient des données dimensionnelles supplémentaires telles que « Component », « cluster », etc.
* Le nouveau plug-in « autocluster » peut aider à dériver cette vision d’une simple requête. 
* Dans l’exemple ci-dessous, vous pouvez voir clairement que chacune des quatre erreurs principales est spécifique à un composant. En outre, alors que les trois erreurs principales sont spécifiques au cluster est renommé db4, la quatrième se produit sur tous les clusters.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Nombre |Pourcentage (%)|Composant|Cluster|Message
|---|---|---|---|---
|7125|26,64|InferenceHostService|EST renommé db4|ExecuteAlgorithmMethod pour la méthode....
|7125|26,64|Composant inconnu|EST renommé db4|Échec de l’appel de InferenceHostService...
|7124|26,64|InferenceAlgorithmExecutor|EST renommé db4|Erreur système inattendue...
|5112|19,11|InferenceAlgorithmExecutor|*|Erreur système inattendue... 

## <a name="map-values-from-one-set-to-another"></a>Mapper des valeurs d’un jeu à un autre

Un cas d’usage courant est le mappage statique de valeurs, qui peut aider à rendre les résultats plus présents.
Par exemple, considérons le tableau suivant. 
`DeviceModel` spécifie un modèle de l’appareil, qui n’est pas une forme très pratique de référencer le nom de l’appareil.  


|DeviceModel |Nombre 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

  
Vous trouverez ci-dessous une meilleure représentation.  

|FriendlyName |Nombre 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Les deux approches ci-dessous montrent comment la représentation peut être obtenue.  

### <a name="mapping-using-dynamic-dictionary"></a>Mapper à l’aide d’un dictionnaire dynamique

L’approche montre le mappage avec un dictionnaire dynamique et des accesseurs dynamiques.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
// Data set definition
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
|iPhone5|66|

### <a name="map-using-static-table"></a>Mapper à l’aide d’une table statique

L’approche montre le mappage avec une table persistante et un opérateur de jointure.
 
Créez la table de mappage une seule fois.

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

Contenu des appareils maintenant.

|DeviceModel |FriendlyName 
|---|---
|iPhone5, 1 |iPhone 5 
|iPhone3, 2 |iPhone 4 
|iPhone7, 2 |iPhone 6 
|iPhone5, 2 |iPhone5 

Utilisez la même astuce pour créer une source de table de test.

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```

Join et Project.

```kusto
Devices  
| join (Source) on DeviceModel  
| project FriendlyName, Count
```

Résultat :

|FriendlyName |Nombre 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Créer et utiliser des tables de dimension de temps de requête

Vous souhaiterez souvent joindre les résultats d’une requête avec une table de dimension ad hoc qui n’est pas stockée dans la base de données. Il est possible de définir une expression dont le résultat est une table dont l’étendue est limitée à une seule requête. Par exemple :

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

Voici un exemple légèrement plus complexe.

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
* `ID`colonne qui identifie l’entité à laquelle chaque ligne est associée, telle qu’un ID d’utilisateur ou un ID de nœud.
* `timestamp`colonne qui fournit la référence d’heure pour la ligne
* autres colonnes

Une requête qui retourne les deux derniers enregistrements pour chaque valeur de la `ID` colonne, où « latest » est défini comme « ayant la valeur la plus élevée `timestamp` », peut être créé avec l' [opérateur de niveau supérieur](topnestedoperator.md).

Par exemple :

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

La numérotation des notes ci-dessous fait référence aux nombres de l’exemple de code, à l’extrême droite.

1. Le `datatable` est un moyen de générer des données de test à des fins de démonstration. Normalement, vous utilisez des données réelles ici.
1. Cette ligne signifie essentiellement « retourner toutes les valeurs distinctes de `id` ».
1. Cette ligne retourne ensuite, pour les deux premiers enregistrements qui maximisent :
     * la `timestamp` colonne
     * les colonnes du niveau précédent (ici, juste `id` )
     * colonne spécifiée à ce niveau (ici, `timestamp` )
1. Cette ligne ajoute les valeurs de la `bla` colonne pour chacun des enregistrements retournés par le niveau précédent. Si la table contient d’autres colonnes intéressantes, vous pouvez répéter cette ligne pour chaque colonne de ce type.
1. Cette dernière ligne utilise l' [opérateur Project-Away](projectawayoperator.md) pour supprimer les colonnes « supplémentaires » introduites par `top-nested` .

## <a name="extend-a-table-with-some-percent-of-total-calculation"></a>Étendre une table avec un calcul en pourcentage du total

Une expression tabulaire qui comprend une colonne numérique est plus utile pour l’utilisateur lorsqu’il est accompagné, avec sa valeur en pourcentage du total. Par exemple, supposons qu’il existe une requête qui produit le tableau suivant :

|SomeSeries|SomeInt|
|----------|-------|
|Foo       |    100|
|Barres       |    200|

Si vous souhaitez afficher cette table comme suit :

|SomeSeries|SomeInt|Remise |
|----------|-------|----|
|Foo       |    100|33,3|
|Barres       |    200|66,6|

Vous devez ensuite calculer le total (Sum) de la `SomeInt` colonne, puis diviser chaque valeur de cette colonne par le total. Pour les résultats arbitraires [, utilisez l’opérateur as](asoperator.md).

```kusto
// The following table literal represents a long calculation
// that ends up with an anonymous tabular value:
datatable (SomeInt:int, SomeSeries:string) [
  100, "Foo",
  200, "Bar",
]
// We now give this calculation a name ("X"):
| as X
// Having this name we can refer to it in the sub-expression
// "X | summarize sum(SomeInt)":
| extend Pct = 100 * bin(todouble(SomeInt) / toscalar(X | summarize sum(SomeInt)), 0.001)
```

## <a name="perform-aggregations-over-a-sliding-window"></a>Effectuer des agrégations sur une fenêtre glissante

L’exemple suivant montre comment synthétiser des colonnes à l’aide d’une fenêtre glissante.
Utilisez le tableau ci-dessous, qui contient les prix des fruits par horodateurs. Calculez les coûts min, Max et Sum de chaque fruit par jour, à l’aide d’une fenêtre glissante de sept jours. Chaque enregistrement dans le jeu de résultats regroupe les sept jours précédents et le résultat contient un enregistrement par jour dans la période d’analyse.  

Table fruits :

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

Requête d’agrégation de fenêtre glissante.
Une explication suit les résultats de la requête :

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

Détails de la requête :

La requête « s’étire » (duplique) chaque enregistrement dans la table d’entrée pendant les sept jours suivant son apparence réelle. Chaque enregistrement apparaît en réalité sept fois.
Par conséquent, l’agrégation quotidienne comprend tous les enregistrements des sept jours précédents.

La numérotation détaillée ci-dessous fait référence aux nombres de l’exemple de code, à l’extrême droite :
1. Compartimenter chaque enregistrement à un jour (par rapport à _start). 
2. Déterminez la fin de la plage par enregistrement-_bin + 7D, sauf si elle est en dehors de la plage _(début, fin)_ , auquel cas elle est ajustée. 
3. Pour chaque enregistrement, créez un tableau de sept jours (horodateurs), en commençant au jour de l’enregistrement actuel. 
4. MV-développez le tableau, en dupliquant donc chaque enregistrement sur sept enregistrements, 1 jour de plus. 
5. Exécutez la fonction d’agrégation pour chaque jour. En raison de #4, cela _résume les sept derniers jours_ . 
6. Les données des sept premiers jours sont incomplètes. Il n’existe aucune période de lookback 7D pour les sept premiers jours. Les sept premiers jours sont exclus du résultat final. Dans l’exemple, ils participent uniquement à l’agrégation pour 2018-10-01.

## <a name="find-preceding-event"></a>Rechercher l’événement précédent
L’exemple suivant montre comment rechercher un événement précédent entre deux jeux de données.  

*Objectif :*: il existe deux jeux de données, a et B. Pour chaque enregistrement dans B, recherchez son événement précédent dans un (autrement dit, l’enregistrement arg_max dans un qui est toujours « plus ancien » que B). Voici la sortie attendue pour les exemples de jeux de données suivants. 

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

Il existe deux approches différentes suggérées pour résoudre ce problème. Vous devez tester les deux sur votre jeu de données spécifique, afin de trouver celui qui convient le mieux à vos besoins.

> [!NOTE] 
> Chaque méthode peut s’exécuter différemment sur des jeux de données différents.

### <a name="suggestion-1"></a>Suggestion #1

Cette suggestion sérialise les jeux de données par ID et horodateur, puis regroupe tous les événements B avec tous les événements précédents et sélectionne le `arg_max` en dehors de tous les dans le groupe.

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

### <a name="suggestion-2"></a>Suggestion #2

Cette suggestion requiert un nombre maximal de lookback-point (le volume de l’enregistrement le plus ancien dans un peut être, par rapport à B). La méthode joint ensuite les deux jeux de données sur l’ID et cette période lookback. La jointure produit tous les candidats possibles, tous les enregistrements A antérieurs à B et au cours de la période lookback, puis le plus proche de B est filtré par arg_min (TimestampB – Timestampa). Plus la période lookback est petite, plus les résultats de la requête sont performants.

Dans l’exemple ci-dessous, la période lookback est définie sur 1m, et l’enregistrement avec l’ID’z’n’a pas d’événement’A’correspondant, car son « A » est plus ancien par 2m.

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

|Id|B_Timestamp|A_Timestamp|EventB|Événement|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|o|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||
