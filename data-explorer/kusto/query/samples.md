---
title: Exemples-Azure Explorateur de données
description: Cet article décrit des exemples dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/08/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b2b304d012ea541f6855091874be8ea5483fae63
ms.sourcegitcommit: b6f0f112b6ddf402e97c011a902bd70ba408e897
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94497554"
---
# <a name="samples"></a>Exemples

::: zone pivot="azuredataexplorer"

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

:::image type="content" source="images/samples/040.png" alt-text="Tableau répertoriant les résultats, avec les colonnes correspondant à l’heure de début, au client I P, à la durée, à la ville et à l’arrêt le plus ancien pour chaque combinaison d’heure de démarrage du client."::: 

Ajoutez du code pour compter les durées dans des emplacements facilement dimensionnés. Dans cet exemple, en raison d’une préférence pour un graphique à barres, divisez par `1s` pour convertir les intervalles en nombres. 

```
    // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 
```

:::image type="content" source="images/samples/050.png" alt-text="Histogramme représentant le nombre de sessions avec des durées dans les plages spécifiées. Plus de 400 sessions ont dépassé 10 secondes. Moins de 100 étaient de 290 secondes.":::

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

|Count|Modèle
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

|Count |Pourcentage (%)|Composant|Cluster|Message
|---|---|---|---|---
|7125|26,64|InferenceHostService|EST renommé db4|ExecuteAlgorithmMethod pour la méthode....
|7125|26,64|Composant inconnu|EST renommé db4|Échec de l’appel de InferenceHostService...
|7124|26,64|InferenceAlgorithmExecutor|EST renommé db4|Erreur système inattendue...
|5112|19,11|InferenceAlgorithmExecutor|*|Erreur système inattendue... 

## <a name="map-values-from-one-set-to-another"></a>Mapper des valeurs d’un jeu à un autre

Un cas d’usage courant est le mappage statique de valeurs, qui peut aider à rendre les résultats plus présents.
Par exemple, considérons le tableau suivant. 
`DeviceModel` spécifie un modèle de l’appareil, qui n’est pas une forme très pratique de référencer le nom de l’appareil.  


|DeviceModel |Count 
|---|---
|iPhone5, 1 |32 
|iPhone3, 2 |432 
|iPhone7, 2 |55 
|iPhone5, 2 |66 

  
Vous trouverez ci-dessous une meilleure représentation.  

|FriendlyName |Count 
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

|FriendlyName|Count|
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

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 


## <a name="create-and-use-query-time-dimension-tables"></a>Créer et utiliser des tables de dimension de temps de requête

Vous souhaiterez souvent joindre les résultats d’une requête avec une table de dimension ad hoc qui n’est pas stockée dans la base de données. Il est possible de définir une expression dont le résultat est une table dont l’étendue est limitée à une seule requête. Exemple :

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

Exemple :

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

*Objectif :* : il existe deux jeux de données, a et B. Pour chaque enregistrement dans B, recherchez son événement précédent dans un (autrement dit, l’enregistrement arg_max dans un qui est toujours « plus ancien » que B). Voici la sortie attendue pour les exemples de jeux de données suivants. 

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

|Timestamp|ID|Événement|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|o|B|
|2019-01-01 00:02:00.0000000|z|B|

Sortie attendue : 

|ID|Timestamp|EventB|A_Timestamp|Événement|
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

::: zone-end

::: zone pivot="azuremonitor"

## <a name="string-operations"></a>Opérations de chaîne
Les sections suivantes fournissent des exemples qui utilisent des chaînes dans le langage de requête Kusto.

### <a name="strings-and-escaping-them"></a>Chaînes et échappement
Les valeurs de chaîne sont entourées de guillemets simples ou doubles. La barre oblique inverse (\\) est utilisée pour échapper les caractères vers le caractère suivant, comme \t pour la tabulation, \n pour le saut de ligne et \" pour le guillemet lui-même.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Pour empêcher « \\ » d’agir comme un caractère d’échappement, ajoutez « \@ » en tant que préfixe à la chaîne :

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


### <a name="string-comparisons"></a>Comparaisons de chaînes

Opérateur       |Description                         |Respecte la casse|Exemple (génère `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Égal à                              |Oui           |`"aBc" == "aBc"`
`!=`           |Non égal à                          |Oui           |`"abc" != "ABC"`
`=~`           |Égal à                              |Non            |`"abc" =~ "ABC"`
`!~`           |Non égal à                          |Non            |`"aBc" !~ "xyz"`
`has`          |Le terme de droite est un terme entier dans le terme de gauche |Non|`"North America" has "america"`
`!has`         |Le terme de droite n’est pas un terme entier dans le terme de gauche       |Non            |`"North America" !has "amer"` 
`has_cs`       |Le terme de droite est un terme entier dans le terme de gauche |Oui|`"North America" has_cs "America"`
`!has_cs`      |Le terme de droite n’est pas un terme entier dans le terme de gauche       |Oui            |`"North America" !has_cs "amer"` 
`hasprefix`    |Le terme de droite est un préfixe dans le terme de gauche         |Non            |`"North America" hasprefix "ame"`
`!hasprefix`   |Le terme de droite n’est pas un préfixe dans le terme de gauche     |Non            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Le terme de droite est un préfixe dans le terme de gauche         |Oui            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Le terme de droite n’est pas un préfixe dans le terme de gauche     |Oui            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Le terme de droite est un suffixe dans le terme de gauche         |Non            |`"North America" hassuffix "ica"`
`!hassuffix`   |Le terme de droite n’est pas un suffixe dans le terme de gauche     |Non            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Le terme de droite est un suffixe dans le terme de gauche         |Oui            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Le terme de droite n’est pas un suffixe dans le terme de gauche     |Oui            |`"North America" !hassuffix_cs "icA"`
`contains`     |Le terme de droite est une sous-séquence du terme de gauche  |Non            |`"FabriKam" contains "BRik"`
`!contains`    |Le terme de droite ne se produit pas dans le terme de gauche           |Non            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Le terme de droite est une sous-séquence du terme de gauche  |Oui           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Le terme de droite ne se produit pas dans le terme de gauche           |Oui           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Le terme de droite est une sous-séquence initiale du terme de gauche|Non            |`"Fabrikam" startswith "fab"`
`!startswith`  |Le terme de droite n’est pas une sous-séquence initiale du terme de gauche|Non        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Le terme de droite est une sous-séquence initiale du terme de gauche|Oui            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Le terme de droite n’est pas une sous-séquence initiale du terme de gauche|Oui        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Le terme de droite est une sous-séquence finale du terme de gauche|Non             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Le terme de droite n’est pas une sous-séquence finale du terme de gauche|Non         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Le terme de droite est une sous-séquence finale du terme de gauche|Oui             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Le terme de droite n’est pas une sous-séquence finale du terme de gauche|Oui         |`"Fabrikam" !endswith "brik"`
`matches regex`|Le terme de gauche contient une correspondance du terme de droite        |Oui           |`"Fabrikam" matches regex "b.*k"`
`in`           |Égal à l’un des éléments       |Oui           |`"abc" in ("123", "345", "abc")`
`!in`          |N’est égal à aucun des éléments   |Oui           |`"bca" !in ("123", "345", "abc")`


### <a name="countof"></a>countof

Compte les occurrences d’une sous-chaîne dans une chaîne. Peut correspondre à des chaînes simples ou utiliser des expressions régulières. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

```
countof(text, search [, kind])
```

- `text` - Chaîne d’entrée 
- `search` - Chaîne simple ou expression régulière à faire correspondre au texte.
- `kind` - _normal_ | _regex_ (par défaut : normal).

Retourne le nombre de fois où la chaîne de recherche peut être mise en correspondance dans le conteneur. Les correspondances de chaînes simples peuvent se chevaucher, mais pas les correspondances d’expression régulière.

#### <a name="plain-string-matches"></a>Correspondances de chaînes simples

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Correspondances d’expression régulière

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


### <a name="extract"></a>extract

Obtient une correspondance pour une expression régulière à partir d’une chaîne donnée. Peut également convertir la sous-chaîne extraite selon le type indiqué.

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

- `regex` - Expression régulière.
- `captureGroup` - Constante entière positive identifiant le groupe de capture à extraire. 0 pour la correspondance entière, 1 pour la valeur mise en correspondance par la première '('parenthèse')' dans l’expression régulière, 2 ou plus pour les parenthèses suivantes.
- `text` - Chaîne à rechercher.
- `typeLiteral` - Littéral de type facultatif (par exemple, typeof(long)). Si elle est fournie, la sous-chaîne extraite est convertie dans ce type.

Retourne la sous-chaîne mise en correspondance avec le captureGroup du groupe de capture indiqué, éventuellement convertie en typeLiteral.
Si aucune correspondance n’est trouvée ou si la conversion de type échoue, la valeur null est retournée.


L’exemple suivant extrait le dernier octet de *ComputerIP* à partir d’un enregistrement de pulsation :
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

L’exemple suivant extrait le dernier octet, le caste en type *real* (nombre) et calcule la valeur d’adresse IP suivante
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

Dans l’exemple ci-dessous, une définition de « Duration » est recherchée dans la chaîne *Trace*. La correspondance est castée en *real* et multipliée par une constante de temps (1 s) *qui caste Duration en type timespan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


### <a name="isempty-isnotempty-notempty"></a>isempty, isnotempty, notempty

- *isempty* retourne true si l’argument est une chaîne vide ou null (voir aussi *isnull* ).
- *isnotempty* retourne true si l’argument n’est pas une chaîne vide ou null (voir aussi *isnotnull* ). alias : *notempty*.


```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Exemples

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


### <a name="parseurl"></a>parseurl

Fractionne une URL en plusieurs parties (protocole, hôte, port, etc.) et retourne un objet dictionnaire qui contient les parties en tant que chaînes.

```
parseurl(urlstring)
```

#### <a name="examples"></a>Exemples

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Le résultat sera :
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


### <a name="replace"></a>remplacer

Remplace toutes les correspondances d’expression régulière par une autre chaîne. 

```
replace(regex, rewrite, input_text)
```

- `regex` - Expression régulière à mettre en correspondance. Elle peut contenir des groupes de capture entre '('parenthèses')'.
- `rewrite` - Expression régulière de remplacement pour toute correspondance trouvée par l’expression régulière correspondante. Utilisez \0 pour faire référence à la correspondance complète, \1 pour le premier groupe de capture, \2 et ainsi de suite pour les groupes de capture suivants.
- `input_text` - Chaîne d’entrée dans laquelle effectuer une recherche.

Retourne le texte après le remplacement de toutes les correspondances d’expressions régulières par des évaluations de réécriture. Les correspondances ne se chevauchent pas.


```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Peut présenter les résultats suivants :

Activité                                        |replaced
------------------------------------------------|----------------------------------------------------------
4663 - Une tentative d’accès à un objet a été effectuée  |ID d’activité 4663 : Une tentative d’accès à un objet a été effectuée.


### <a name="split"></a>split

Fractionne une chaîne donnée en fonction d’un délimiteur spécifié et retourne un tableau des sous-chaînes obtenues.

```
split(source, delimiter [, requestedIndex])
```

- `source` - Chaîne à fractionner en fonction du délimiteur spécifié.
- `delimiter` - Délimiteur à utiliser pour fractionner la chaîne source.
- `requestedIndex` - Index de base zéro facultatif. S’il est fourni, le tableau de chaînes retourné contient uniquement cet élément (s’il existe).


#### <a name="examples"></a>Exemples

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

### <a name="strcat"></a>strcat

Concatène les arguments de chaîne (prend en charge 1 à 16 arguments).

```
strcat("string1", "string2", "string3")
```

#### <a name="examples"></a>Exemples
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


### <a name="strlen"></a>strlen

Retourne la longueur d'une chaîne.

```
strlen("text_to_evaluate")
```

#### <a name="examples"></a>Exemples
```Kusto
print strlen("hello")   // result: 5
```


### <a name="substring"></a>substring

Extrait une sous-chaîne d’une chaîne source donnée à partir de l’index indiqué. Éventuellement, la longueur de la sous-chaîne demandée peut être spécifiée.

```
substring(source, startingIndex [, length])
```

- `source` - Chaîne source dont la sous-chaîne est extraite.
- `startingIndex` - Position du caractère de départ (base zéro) de la sous-chaîne demandée.
- `length` - Paramètre facultatif qui permet de spécifier la longueur demandée de la sous-chaîne retournée.

#### <a name="examples"></a>Exemples
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


### <a name="tolower-toupper"></a>tolower, toupper

Convertit une chaîne donnée en minuscules ou en majuscules.

```
tolower("value")
toupper("value")
```

#### <a name="examples"></a>Exemples
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```

## <a name="date-and-time-operations"></a>Opérations Date/heure
Les sections suivantes fournissent des exemples qui utilisent des valeurs de date et d’heure dans le langage de requête Kusto.

### <a name="date-time-basics"></a>Notions de base de date et d’heure
Le langage de requête de Kusto comporte deux principaux types de données associés aux dates et heures : datetime et timespan. Toutes les dates sont exprimées au format UTC. Même si plusieurs formats datetime sont pris en charge, le format ISO8601 est préféré. 

Les types timespan sont exprimés en tant que valeur décimale suivie d’une unité de temps :

|abréviation   | unité de temps    |
|:---|:---|
|d           | day          |
|h           | hour         |
|m           | minute       |
|s           | second       |
|ms          | milliseconde  |
|microseconde | microseconde  |
|graduation        | nanoseconde   |

Les types datetime peuvent être créés en castant une chaîne avec l’opérateur `todatetime`. Par exemple, pour passer en revue les pulsations de machine virtuelle envoyées dans un laps de temps spécifique, utilisez l’opérateur `between` pour spécifier une plage de temps.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Un autre scénario courant est la comparaison d’une valeur datetime au présent. Par exemple, pour afficher toutes les pulsations au cours des deux dernières minutes, vous pouvez utiliser l’opérateur `now` avec une valeur timespan qui représente deux minutes :

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Un raccourci est également disponible pour cette fonction :
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

La méthode la plus rapide et la plus lisible consiste cependant à utiliser l’opérateur `ago` :
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Supposons qu’au lieu de connaître l’heure de début et de fin, vous connaissiez l’heure de début et la durée. Vous pouvez réécrire la requête comme suit :

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

### <a name="converting-time-units"></a>Conversion des unités de temps
Il peut être utile d’exprimer une valeur datetime ou timespan dans une unité de temps autre que celle par défaut. Par exemple, supposons que vous examinez les événements d’erreur des 30 dernières minutes et avez besoin d’une colonne calculée qui affiche le temps écoulé depuis que l’événement s’est produit :

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

La `timeAgo` colonne contient des valeurs telles que : « 00:09:31.5118992 », ce qui signifie qu’elles sont au format hh : mm : SS. fffffff. Si vous souhaitez mettre en forme ces valeurs selon le `numver` de minutes depuis l’heure de début, divisez cette valeur par « 1 minute » :

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


### <a name="aggregations-and-bucketing-by-time-intervals"></a>Agrégations et création de compartiments par intervalles de temps
Un autre scénario courant est la nécessité d’obtenir des statistiques sur une certaine période de temps dans un fragment de temps particulier. Pour ce scénario, un opérateur `bin` peut être utilisé dans le cadre d’une clause de synthèse.

Utilisez la requête suivante pour obtenir le nombre d’événements qui se sont produits toutes les 5 minutes pendant la dernière demi-heure :

```Kusto
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

Une autre façon de créer des compartiments de résultats consiste à utiliser des fonctions, telles que `startofday` :

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Cette requête produit les résultats suivants :

|timestamp|count_|
|--|--|
|2018-07-28T00:00:00.000|7 136|
|2018-07-29T00:00:00.000|12 315|
|2018-07-30T00:00:00.000|16 847|
|2018-07-31T00:00:00.000|12 616|
|2018-08-01T00:00:00.000|5 416|


### <a name="time-zones"></a>Fuseaux horaires
Dans la mesure où toutes les valeurs datetime sont exprimées au format UTC, il est souvent utile de convertir ces valeurs dans le fuseau horaire local. Par exemple, utilisez ce calcul pour convertir les heures UTC en PST :

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="aggregations"></a>Agrégations
Les sections suivantes donnent des exemples d’agrégation des résultats d’une requête dans le langage de requête Kusto.

### <a name="count"></a>count
Comptez le nombre de lignes du jeu de résultats une fois que tous les filtres sont appliqués. L’exemple suivant retourne le nombre total de lignes dans la table _Perf_ au cours des 30 dernières minutes. Le résultat est retourné dans une colonne nommée *count_* , sauf si vous lui attribuez un nom spécifique :


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Une visualisation de graphique temporel peut être utile pour voir une tendance au fil du temps :

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

La sortie de cet exemple affiche la courbe de tendance du nombre d’enregistrements dans la table Perf dans des intervalles de 5 minutes :

![Tendance du nombre](images/samples/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Utilisez `dcount` et `dcountif` pour compter des valeurs distinctes dans une colonne spécifique. La requête suivante évalue le nombre d’ordinateurs distincts qui ont envoyé des pulsations dans la dernière heure :

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Pour compter uniquement les ordinateurs Linux qui ont envoyé des pulsations, utilisez `dcountif` :

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Évaluation des sous-groupes
Pour effectuer un compte ou d’autres agrégations de sous-groupes dans vos données, utilisez le mot clé `by`. Par exemple, pour compter le nombre d’ordinateurs Linux distincts qui ont envoyé des pulsations dans chaque pays/région :

```Kusto
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


Pour analyser des sous-groupes encore plus petits de vos données, ajoutez des noms de colonnes supplémentaires à la section `by`. Par exemple, vous pouvez peut-être compter le nombre d’ordinateurs distincts de chaque pays/région par OSType :

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```


### <a name="percentile"></a>Percentile
Pour rechercher la valeur médiane, utilisez la fonction `percentile` avec une valeur pour spécifier le centile :

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Vous pouvez également spécifier différents centiles afin d’obtenir un résultat agrégé pour chacun :

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Cela peut indiquer que quelques UC d’ordinateur ont des valeurs médianes similaires mais, si certains sont stables autour de la valeur médiane, d’autres ordinateurs ont signalé des valeurs d’UC beaucoup plus faibles et beaucoup plus élevées, ce qui signifie la présence de pics.

### <a name="variance"></a>Variance
Pour évaluer directement la variance d’une valeur, utilisez les méthodes de variance et d’écart type :

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

Un bon moyen d’analyser la stabilité de l’utilisation de l’UC consiste à combiner stdev avec le calcul de la valeur médiane :

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

### <a name="generating-lists-and-sets"></a>Génération de listes et d’ensembles
Vous pouvez utiliser `makelist` pour déplacer des données selon l’ordre des valeurs dans une colonne particulière. Par exemple, vous voulez explorer l’ordre le plus courant dans lequel les événements se produisent sur vos ordinateurs. Vous pouvez avant tout déplacer les données selon l’ordre des EventID sur chaque ordinateur. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` génère une liste dans l’ordre dans lequel les données lui ont été passées. Pour trier les événements du plus ancien au plus récent, utilisez `asc` dans l’instruction d’ordre au lieu de `desc`. 

Il est également utile de créer une liste de valeurs distinctes seulement. Il s’agit d’un _ensemble_ qui peut être généré avec `makeset` :

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Comme `makelist`, `makeset` fonctionne également avec des données ordonnées et génère les tableaux selon l’ordre des lignes qui lui sont passées.

### <a name="expanding-lists"></a>Extension de listes
L’opération inverse de `makelist` ou `makeset` est `mvexpand`, qui étend une liste de valeurs sur des lignes séparées. L’extension peut porter sur n’importe quel nombre de colonnes dynamiques, JSON et de tableau. Par exemple, vous pouvez rechercher dans la table *Heartbeat* des solutions envoyant des données à partir d’ordinateurs qui ont envoyé une pulsation dans la dernière heure :

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Solutions | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... |

Utilisez `mvexpand` pour afficher chaque valeur dans une ligne distincte au lieu d’une liste séparée par des virgules :

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

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


Vous pouvez ensuite utiliser à nouveau `makelist` pour regrouper les éléments et afficher cette fois la liste des ordinateurs par solution :

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Solutions | list_Computer |
|--------------|----------------------|
| "security" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

### <a name="handling-missing-bins"></a>Gestion des compartiments manquants
Une application utile de `mvexpand` est le besoin de remplir les valeurs par défaut dans pour les emplacements manquants. Supposons, par exemple, que vous recherchez le temps d’activité d’une machine particulière en explorant sa pulsation. Vous voulez aussi voir la source de la pulsation qui se trouve dans la colonne _Catégorie_. En règle générale, nous utiliserions une simple instruction de synthèse comme suit :

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent direct | 2017-06-06T17:00:00Z | 15 |
| Agent direct | 2017-06-06T18:00:00Z | 60 |
| Agent direct | 2017-06-06T20:00:00Z | 55 |
| Agent direct | 2017-06-06T21:00:00Z | 57 |
| Agent direct | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Dans ces résultats, cependant, le compartiment associé à "2017-06-06T19:00:00Z" est manquant, car il n’existe pas de données de pulsation pour cette heure. Utilisez la fonction `make-series` pour affecter une valeur par défaut aux compartiments vides. Cette opération génère une ligne pour chaque catégorie avec deux colonnes de tableau supplémentaires, une pour les valeurs et une pour les intervalles de temps correspondants :

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Category | count_ | TimeGenerated |
|---|---|---|
| Agent direct | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

Le troisième élément du tableau *count_* est égal à 0 comme prévu, et il existe un horodatage correspondant de "2017-06-06T19:00:00.0000000Z" dans le tableau _TimeGenerated_. Ce format de tableau est cependant difficile à lire. Utilisez `mvexpand` pour étendre les tableaux et produire le même format de sortie que celui généré par `summarize` :

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Category | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Agent direct | 2017-06-06T17:00:00Z | 15 |
| Agent direct | 2017-06-06T18:00:00Z | 60 |
| Agent direct | 2017-06-06T19:00:00Z | 0 |
| Agent direct | 2017-06-06T20:00:00Z | 55 |
| Agent direct | 2017-06-06T21:00:00Z | 57 |
| Agent direct | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



### <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Restriction des résultats à un ensemble d’éléments : `let`, `makeset`, `toscalar`, `in`
Un scénario courant consiste à sélectionner les noms de certaines entités spécifiques selon un ensemble de critères, puis à filtrer un autre jeu de données selon cet ensemble d’entités. Par exemple, vous pouvez rechercher les ordinateurs qui sont connus comme ayant des mises à jour manquantes et identifier les adresses IP auxquelles ces ordinateurs font appel :


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="joins"></a>Jointures
Les jointures vous permettent d’analyser les données provenant de plusieurs tables, dans la même requête. Elles fusionnent les lignes de deux jeux de données en faisant correspondre les valeurs des colonnes spécifiées.


```Kusto
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

Dans cet exemple, le premier jeu de données filtre tous les événements de connexion. Un deuxième jeu de données se joint au premier, qui filtre tous les événements de déconnexion. Les colonnes projetées sont _Computer_ , _Account_ , _TargetLogonId_ et _TimeGenerated_. Les jeux de données sont corrélés par une colonne partagée, _TargetLogonId_. La sortie est un seul enregistrement par corrélation, avec à la fois l’heure de connexion et de déconnexion.

Si les deux jeux de données ont des colonnes portant le même nom, les colonnes du jeu de données de droite reçoivent un numéro d’index. Ainsi, les résultats dans cet exemple affichent _TargetLogonId_ avec les valeurs de la table de gauche et _TargetLogonId1_ avec les valeurs de la table de droite. Dans ce cas, la deuxième colonne _TargetLogonId1_ a été supprimée à l’aide de l’opérateur `project-away`.

> [!NOTE]
> Pour améliorer les performances, conservez uniquement les colonnes appropriées des jeux de données joints à l’aide de l’opérateur `project`.


Utilisez la syntaxe suivante pour joindre deux jeux de données et la clé jointe porte un nom différent entre les deux tables :
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

### <a name="lookup-tables"></a>Tables de choix
Une utilisation courante des jointures est l’emploi de mappage statique de valeurs à l’aide de `datatable` qui permet de rendre les résultats plus présentables, par exemple pour enrichir les données d’événements de sécurité avec le nom de l’événement pour chaque ID d’événement.

```Kusto
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

| eventName | count_ |
|:---|:---|
| Le descripteur d’un objet a été fermé | 290 995 |
| Un handle vers un objet a été demandé | 154 157 |
| Une tentative a été effectuée pour dupliquer un handle vers un objet | 144 305 |
| Une tentative d’accès à un objet a été effectuée | 123 669 |
| Opération de chiffrement | 153 495 |
| Opération de fichier de clé | 153 496 |

## <a name="json-and-data-structures"></a>JSON et les structures de données

Les objets imbriqués sont des objets qui contiennent d’autres objets dans un tableau ou un mappage de paires clé-valeur. Ces objets sont représentés sous forme de chaînes JSON. Cette section décrit comment JSON est utilisé pour récupérer des données et analyser des objets imbriqués.

### <a name="working-with-json-strings"></a>Utilisation de chaînes JSON
Utilisez `extractjson` pour accéder à un élément JSON spécifique dans un chemin connu. Cette fonction nécessite une expression de chemin qui utilise les conventions suivantes.

- _$_ pour faire référence au dossier racine
- Utilisez la notation entre crochets ou sous forme de points pour faire référence aux index et aux éléments, comme illustré dans les exemples suivants.


Utilisez des crochets pour les index et des points pour séparer les éléments :

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Voici le même résultat en utilisant uniquement la notation entre crochets :

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

S’il n’existe qu’un seul élément, vous pouvez utiliser uniquement la notation sous forme de points :

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


### <a name="parsejson"></a>parsejson
Pour accéder à plusieurs éléments dans votre structure json, il est plus facile d’y accéder en tant qu’objet dynamique. Utilisez `parsejson` pour caster des données de texte en objet dynamique. Une fois les données converties en un type dynamique, d’autres fonctions peuvent être utilisées pour analyser les données.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Utilisez `arraylength` pour compter le nombre d’éléments dans un tableau :

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Utilisez `mvexpand` pour répartir les propriétés d’un objet en lignes séparées.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![Capture d’écran montrant hosts_0 avec des valeurs pour l’emplacement, l’état et le taux.](images/samples/mvexpand.png)

### <a name="buildschema"></a>buildschema
Utilisez `buildschema` pour obtenir le schéma qui admet toutes les valeurs d’un objet :

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

La sortie est un schéma au format JSON :
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
Cette sortie décrit les noms des champs d’objets et leurs types de données correspondants. 

Les objets imbriqués peuvent avoir des schémas différents, comme dans l’exemple suivant :

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

## <a name="charts"></a>Graphiques
Les sections suivantes fournissent des exemples qui utilisent des graphiques dans le langage de requête Kusto.

### <a name="chart-the-results"></a>Graphique des résultats
Commencez par passer en revue le nombre d’ordinateurs par système d’exploitation au cours de la dernière heure :

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Par défaut, les résultats sont affichés sous forme de table :

![Table de charge de travail](images/samples/table-display.png)

Pour obtenir un meilleur affichage, sélectionnez **Graphique** , puis choisissez l’option **Secteurs** pour visualiser les résultats :

![Graphique en secteurs](images/samples/charts-and-diagrams-pie.png)


### <a name="timecharts"></a>Graphiques temporels
Affichez la moyenne, le 50e et le 95e centiles de temps processeur dans des intervalles de 1 heure. La requête génère plusieurs séries et vous pouvez ensuite sélectionner les séries à afficher dans le graphique de temps :

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Sélectionnez l’option d’affichage graphique en **courbes** :

![Graphique en courbes](images/samples/charts-and-diagrams-multiple-series.png)

#### <a name="reference-line"></a>Ligne de référence

Une ligne de référence peut vous aider à identifier facilement si la métrique a dépassé un seuil spécifique. Pour ajouter une ligne à un graphique, étendez le jeu de données avec une colonne constante :

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Ligne de référence](images/samples/charts-and-diagrams-multiple-series-threshold.png)

### <a name="multiple-dimensions"></a>Plusieurs dimensions
Plusieurs expressions de la clause `by` de `summarize` créent plusieurs lignes dans les résultats, une pour chaque combinaison de valeurs.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Quand vous affichez les résultats sous forme de graphique, il utilise la première colonne de la clause `by`. L’exemple suivant montre un histogramme empilé avec _l’EventID_. Comme les dimensions doivent être de type `string`, dans cet exemple _l’EventID_ est casté en chaîne. 

![EventID de graphique à barres](images/samples/charts-and-diagrams-multiple-dimension-1.png)

Vous pouvez changer de valeur en sélectionnant la liste déroulante avec le nom de colonne. 

![AccountType de graphique à barres](images/samples/charts-and-diagrams-multiple-dimension-2.png)

## <a name="smart-analytics"></a>Analytique intelligente
Cette section contient des exemples qui utilisent des fonctions Smart Analytics dans Log Analytics pour effectuer une analyse de l’activité des utilisateurs. Vous pouvez utiliser ces exemples pour analyser vos propres applications supervisées par Application Insights, ou utiliser les concepts de ces requêtes pour effectuer une analyse similaire sur d’autres données. 

### <a name="cohorts-analytics"></a>Analytique des cohortes

L’analyse des cohortes effectue le suivi de l’activité de groupes d’utilisateurs spécifiques, appelés cohortes. Elle tente de mesurer le degré d’attractivité d’un service en mesurant le taux de retour des utilisateurs. Les utilisateurs sont regroupés d’après le moment où ils ont utilisé pour la première fois le service. Lors de l’analyse des cohortes, nous nous attendons à observer une baisse d’activité sur les premières périodes suivies. Le titre de chaque cohorte correspond à la semaine durant laquelle ses membres ont été observés pour la première fois.

L’exemple suivant analyse le nombre d’activités que les utilisateurs ont effectuées durant les cinq semaines suivant leur première utilisation du service.

```Kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample we calculate distinct users but you can change
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
Cet exemple génère la sortie suivante.

:::image type="content" source="images/samples/cohorts.png" alt-text="Sortie d’analyse de cohorte":::

### <a name="rolling-monthly-active-users-and-user-stickiness"></a>Cumul des utilisateurs actifs mensuels et de l’adhérence utilisateur
Les exemples suivants utilisent l’analyse de série chronologique avec la fonction [series_fir](/azure/kusto/query/series-firfunction), qui vous permet d’effectuer des calculs de fenêtre glissante. L’exemple d’application supervisée est un magasin en ligne qui assure le suivi de l’activité des utilisateurs par le biais d’événements personnalisés. La requête effectue le suivi de deux types d’activités de l’utilisateur, _AddToCart_ et _Checkout_ , et définit les _utilisateurs actifs_ comme ceux ayant effectué un paiement au moins une fois durant un jour donné.



```Kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they performed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked-out in our web site
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column containing a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day)
| mvexpand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// render as timechart
| render timechart
```

Cet exemple génère la sortie suivante.

:::image type="content" source="images/samples/rolling-mau.png" alt-text="Sortie de cumul des utilisateurs mensuels":::

L’exemple suivant convertit la requête ci-dessus en fonction réutilisable et l’utilise pour calculer l’adhérence de l’utilisateur. Les utilisateurs actifs dans cette requête sont définis comme uniquement ceux ayant effectué un paiement au moins une fois durant un jour donné.

``` Kusto
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
    | mvexpand RollingUserClicksByDay
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

Cet exemple génère la sortie suivante.

:::image type="content" source="images/samples/user-stickiness.png" alt-text="Sortie de l’adhérence utilisateur":::

### <a name="regression-analysis"></a>Analyse de régression
Cet exemple montre comment créer un détecteur automatisé pour les interruptions de service basé exclusivement sur les journaux d’activité des traces d’une application. Le détecteur recherche les augmentations soudaines et anormales dans la quantité relative de traces d’erreur et d’avertissement dans l’application.

Deux techniques sont utilisées pour évaluer l’état du service en fonction des données de journaux d’activité des traces :

- Utilisez [make-series](/azure/kusto/query/make-seriesoperator) pour convertir les journaux d’activité des traces textuels semi-structurés en une métrique qui représente le rapport entre les lignes de traces positives et négatives.
- Utilisez [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) et [series_fit_line](/azure/kusto/query/series-fit-linefunction) pour effectuer une détection de saut avancée grâce à une analyse de série chronologique avec une régression linéaire de deux lignes.

``` Kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease)
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```


## <a name="next-steps"></a>Étapes suivantes

- [Passez en revue un didacticiel sur le langage de requête Kusto](tutorial.md?pivots=azuremonitor).


::: zone-end

