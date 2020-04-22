---
title: Échantillons - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit échantillons dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 746017d41b5f1a13ce73f2c27df9cac5b5982ff0
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663594"
---
# <a name="samples"></a>Exemples

Voici quelques besoins de requête commune et comment le langage de requête Kusto peut être utilisé pour les répondre.

## <a name="display-a-column-chart"></a>Afficher un graphique de colonne

Projetez deux colonnes ou plus et utilisez-les comme axe x et y d’un graphique :

```kusto 
StormEvents
| where isnotempty(EndLocation) 
| summarize event_count=count() by EndLocation
| top 10 by event_count
| render columnchart
```

* La première colonne forme l’axe x. Il peut s’agir de numérique, d’heure ou de ficelle. 
* `where`Utilisez, `summarize` `top` et pour limiter le volume de données que vous affichez.
* Trier les résultats afin de définir l’ordre de l’axe x.

:::image type="content" source="images/samples/060.png" alt-text="060":::

<a name="activities"></a>
## <a name="get-sessions-from-start-and-stop-events"></a>Obtenir des sessions à partir d’événements de démarrage et d’arrêt

Imaginons un journal d’événements, dans lequel certains événements marquent le début ou la fin d’une session ou d’une activité étendue. 

|Nom|City|SessionId|Timestamp|
|---|---|---|---|
|Démarrer|London|2817330|2015-12-09T10:12:02.32|
|Game|London|2817330|2015-12-09T10:12:52.45|
|Démarrer|Manchester|4267667|2015-12-09T10:14:02.23|
|Arrêter|London|2817330|2015-12-09T10:23:43.18|
|Annuler|Manchester|4267667|2015-12-09T10:27:26.29|
|Arrêter|Manchester|4267667|2015-12-09T10:28:31.72|

Chaque événement ayant un ID de session, le problème est de faire correspondre les événements de début et d’arrêt portant le même identifiant.

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

Utilisez [`let`](./letstatement.md) pour nommer une projection de la table qui est épilée dans la mesure du possible avant d’entrer dans la jointure.
[`Project`](./projectoperator.md)est utilisé pour changer les noms des timestamps de sorte que les temps de départ et d’arrêt peuvent apparaître dans le résultat. En outre, il sélectionne les autres colonnes que nous souhaitons voir dans le résultat. [`join`](./joinoperator.md)correspond aux entrées de démarrage et d’arrêt pour la même activité, créant une ligne pour chaque activité. Enfin, `project` ajoute de nouveau une colonne pour afficher la durée de l’activité.


|City|SessionId|StartTime|StopTime|Duration|
|---|---|---|---|---|
|London|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### <a name="get-sessions-without-session-id"></a>Obtenir des sessions, sans ID de session

Supposons maintenant que les événements de démarrage et d’arrêt n’ont pas un ID de session facilitant une mise en correspondance. Toutefois, nous avons une adresse IP du client où la session a eu lieu. En supposant que chaque adresse client effectue une seule session à la fois, nous pouvons établir une correspondance entre chaque événement de démarrage et l’événement d’arrêt suivant à partir de la même adresse IP.

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

La jointure fait correspondre chaque heure de démarrage à toutes les heures d’arrêt à partir de la même adresse IP du client. Ainsi, nous commençons par supprimer les correspondances comportant des heures d’arrêt antérieures.

Ensuite, nous effectuons des regroupements par heure de démarrage et adresse IP pour obtenir un groupe par session. Nous devons `bin` fournir une fonction pour le paramètre StartTime: si nous ne le faisons pas, Kusto utilisera automatiquement des bacs d’une heure, qui correspondront à certains temps de départ avec les mauvais temps d’arrêt.

`arg_min`sélectionne la ligne avec la plus petite `*` durée dans chaque groupe, et le paramètre passe à travers toutes les autres colonnes, bien qu’il préfixe "min_" à chaque nom de colonne. 

:::image type="content" source="images/samples/040.png" alt-text="040"::: 

Ensuite, nous pouvons ajouter un peu de code pour compter les durées dans les bacs de taille commodé. Nous avons une légère préférence pour un graphique `1s` à barres, donc nous nous divisons pour convertir les durées en nombres. 


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Display in a bar chart:
    | sort by duration asc | render barchart 

:::image type="content" source="images/samples/050.png" alt-text="050":::

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

<a name="concurrent-activities"><a/>
## <a name="chart-concurrent-sessions-over-time"></a>Sessions simultanées du graphique au fil du temps

Supposons que nous disposons d’une table d’activités avec des heures de début et de fin pour chacune d’elles.  Nous souhaitons afficher un graphique temporel qui montre le nombre d’activités en cours d’exécution simultanément à un moment donné.

Voici un exemple d’entrée, que nous appellerons `X`:

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

Comme nous voulons un graphique avec emplacements de 1 minute, nous voulons créer quelque chose qui, à chaque intervalle de 1m, peut faire l’objet d’un comptage pour chaque activité en cours d’exécution.

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

Mais au lieu de garder ces tableaux, nous allons les élargir en utilisant [mv-expand:](./mvexpandoperator.md)

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

Nous pouvons maintenant les regrouper par heure d’échantillon, en comptant les occurrences de chaque activité :

```kusto
X
| mv-expand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* Nous avons besoin de temps de démarrage () parce que [mv-expand](./mvexpandoperator.md) donne une colonne de type dynamique.
* En outre, nous devons recourir à bin() car, pour les valeurs numériques et les dates, summarize applique systématiquement une fonction bin avec un intervalle par défaut si vous n’en indiquez aucun. 


| count_SessionId | exemples|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|
| 1 | 10:06:00|

Ce résultat peut être restitué sous forme de graphique à barres ou de graphique temporel.

## <a name="introduce-null-bins-into-summarize"></a>Introduire des bacs nuls dans le résumé

Lorsque `summarize` l’opérateur est appliqué sur une `datetime` clé de groupe qui se compose d’une colonne, on «bins» normalement ces valeurs à des bacs à largeur fixe. Par exemple :

```kusto
let StartTime=ago(12h);
let StopTime=now()
T
| where Timestamp > StartTime and Timestamp <= StopTime 
| where ...
| summarize Count=count() by bin(Timestamp, 5m)
```

Cette opération produit une table avec une seule `T` rangée par groupe de rangées dans cette chute dans chaque bac de cinq minutes. Ce qu’il ne fait pas est d’ajouter "bacs `StartTime` nuls" - rangées pour les valeurs de bac de temps entre et `StopTime` pour lesquels il n’y a pas de ligne correspondante dans `T`. 

Souvent, il est souhaité de "pad" la table avec ces bacs. Voici une façon de le faire:

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

Voici une explication étape par étape de la requête ci-dessus: 

1. L’utilisation de l’opérateur `union` nous permet d’ajouter des lignes supplémentaires à une table. Ces lignes sont produites `union`par l’expression à .
2. Utilisation `range` de l’opérateur pour produire une table ayant une seule rangée / colonne.
   La table n’est pas `mv-expand` utilisée pour autre chose que pour travailler.
3. Utilisation `mv-expand` de l’opérateur sur la `range` fonction pour créer autant de `StartTime` lignes `EndTime`qu’il ya des bacs de 5 minutes entre et .
4. Le tout `Count` `0`avec un de .
5. Enfin, nous `summarize` utilisons l’opérateur pour regrouper les bacs de l’argument original (gauche, ou extérieur) et `union` les bacs de l’argument intérieur à elle (à savoir, les rangées de bacs nuls). Cela garantit que la sortie a une rangée par bac, dont la valeur est soit nulle ou le nombre d’origine.  

## <a name="get-more-out-of-your-data-in-kusto-using-machine-learning"></a>Sortez-en de vos données à Kusto en utilisant l’apprentissage automatique 

Il existe de nombreux cas d’utilisation intéressantes pour tirer parti des algorithmes d’apprentissage automatique et de tirer des informations intéressantes à partir de données de télémétrie. Bien que souvent ces algorithmes nécessitent un jeu de données très structuré comme leur entrée, les données brutes de journal ne correspondent généralement pas à la structure et la taille requises. 

Notre voyage commence par la recherche d’anomalies dans le taux d’erreur d’un service spécifique d’inférences Bing. La table Logs a des enregistrements 65B, et la requête simple ci-dessous filtre 250K erreurs, et crée une série de données chronos de nombre d’erreurs qui utilise la fonction de détection d’anomalies [series_decompose_anomalies](series-decompose-anomaliesfunction.md). Les anomalies sont détectées par le service Kusto, et sont mises en évidence comme des points rouges sur le graphique des séries chronos.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22) and Timestamp < datetime(2015-08-23) 
| where Level == "e" and Service == "Inferences.UnusualEvents_Main" 
| summarize count() by bin(Timestamp, 5min)
| render anomalychart 
```

Le service a identifié peu de seaux de temps avec le taux d’erreur suspect. J’utilise Kusto pour zoomer sur ce laps de temps, en cours d’exécution d’une requête qui s’agrége sur la colonne «Message» en essayant de rechercher les erreurs du haut. J’ai coupé les parties pertinentes de la trace de pile entière du message pour mieux s’adapter à la page. Vous pouvez voir que j’ai eu un grand succès avec les huit premières erreurs, mais a ensuite atteint une longue queue d’erreurs depuis le message d’erreur a été créé par une chaîne de format qui contenait des données changeantes. 

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
|7125|ExécuterAlgorithmMethod pour la méthode 'RunCycleFromInterimData' a échoué...
|7125|InferenceHostService appel a échoué. System.NullReferenceException: Référence d’objet non réglée à une instance d’objet...
|7124|Erreur inattendue du système d’inférence. System.NullReferenceException: Référence d’objet non réglée à une instance d’objet... 
|5112|Erreur inattendue du système d’inférence. System.NullReferenceException: Référence d’objet non définie à une instance d’un objet..
|174|InferenceHostService appel a échoué..System.ServiceModel.CommunicationException: Il y avait une erreur d’écriture à la pipe:...
|10|ExécuterAlgorithmMethod pour la méthode 'RunCycleFromInterimData' a échoué...
|10|Erreur du système d’inférence. Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|3|InferenceHostService appel a échoué..System.ServiceModel.CommunicationObjectFaultedException:...
|1|Erreur du système d’inférence... SocialGraph.BOSS.OperationResponse... AIS TraceId:8292FC561AC64BED8FA243808FE74EFD...
|1|Erreur du système d’inférence... SocialGraph.BOSS.OperationResponse... AIS TraceId: 5F79F7587FF943EC9B641E02E701AFBF...

C’est `reduce` là que l’opérateur vient vous aider. L’opérateur `reduce` a identifié 63 erreurs différentes comme en est venu le même point d’instrumentation de trace dans le code, et m’a aidé à me concentrer sur une trace d’erreur significative supplémentaire dans cette fenêtre de temps.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| reduce by Message with threshold=0.35
| project Count, Pattern
```

|Count|Modèle
|---|---
|7125|ExécuterAlgorithmMethod pour la méthode 'RunCycleFromInterimData' a échoué...
|  7125|InferenceHostService appel a échoué. System.NullReferenceException: Référence d’objet non réglée à une instance d’objet...
|  7124|Erreur inattendue du système d’inférence. System.NullReferenceException: Référence d’objet non réglée à une instance d’objet...
|  5112|Erreur inattendue du système d’inférence. System.NullReferenceException: Référence d’objet non définie à une instance d’un objet..
|  174|InferenceHostService appel a échoué..System.ServiceModel.CommunicationException: Il y avait une erreur d’écriture à la pipe:...
|  63|Erreur du système d’inférence. Microsoft.Bing.Platform.Inferences. \*: \* Écrivez pour écrire à l’Objet BOSS. \*: SocialGraph.BOSS.Reques...
|  10|ExécuterAlgorithmMethod pour la méthode 'RunCycleFromInterimData' a échoué...
|  10|Erreur du système d’inférence. Microsoft.Bing.Platform.Inferences.Service.Managers.UserInterimDataManagerException:...
|  3|InferenceHostService appel a échoué. System.ServiceModel. \*: L’objet, System.ServiceModel.Channels. \* \* \*, \* car est le ... + \*   à Syst...

Maintenant que j’ai une bonne vue sur les erreurs supérieures qui ont contribué aux anomalies détectées, je veux comprendre l’impact de ces erreurs dans mon système. Le tableau 'Logs' contient des données dimensionnelles supplémentaires telles que 'Component', 'Cluster', etc... Le nouveau plugin 'autocluster' peut m’aider à tirer cette perspicacité avec une simple requête. Dans cet exemple ci-dessous, je peux clairement voir que chacune des quatre premières erreurs est spécifique à un composant, et tandis que les trois principales erreurs sont spécifiques à la grappe DB4, la quatrième se produit à travers tous les clusters.

```kusto
Logs
| where Timestamp >= datetime(2015-08-22 05:00) and Timestamp < datetime(2015-08-22 06:00)
| where Level == "e" and Service == "Inferences.UnusualEvents_Main"
| evaluate autocluster()
```

|Count |Pourcentage (%)|Composant|Cluster|Message
|---|---|---|---|---
|7125|26.64|InferenceHostService|DB4|ExécuterAlgorithmMethod pour la méthode ....
|7125|26.64|Composant inconnu|DB4|InferenceHostService appel a échoué ....
|7124|26.64|InférenceAlgorithmExecuteur|DB4|Erreur inattendue du système d’inférence...
|5112|19.11|InférenceAlgorithmExecuteur|*|Erreur inattendue du système d’inférence... 

## <a name="mapping-values-from-one-set-to-another"></a>Cartographier les valeurs d’un ensemble à l’autre

Un cas d’utilisation courant utilise la cartographie statique des valeurs qui peuvent aider à adopter des résultats de manière plus présentable.  
Par exemple, envisagez d’avoir une table suivante. DeviceModel spécifie un modèle de l’appareil, qui n’est pas une forme très pratique de référencement au nom de l’appareil.  


|DeviceModel |Count 
|---|---
|iPhone5,1 |32 
|iPhone3,2 |432 
|iPhone7,2 |55 
|iPhone5,2 |66 

  
Une meilleure représentation peut être :  

|FriendlyName |Count 
|---|---
|iPhone 5 |32 
|iPhone 4 |432 
|iPhone 6 |55 
|iPhone5 |66 

Les deux approches ci-dessous montrent comment cela peut être réalisé.  

### <a name="mapping-using-dynamic-dictionary"></a>Cartographie à l’aide d’un dictionnaire dynamique

L’approche ci-dessous montre comment la cartographie peut être réalisée à l’aide d’un dictionnaire dynamique et d’accesseurs dynamiques.

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



### <a name="mapping-using-static-table"></a>Cartographie à l’aide d’une table statique

L’approche ci-dessous montre comment la cartographie peut être réalisée à l’aide d’une table persistante et d’un opérateur d’adhésion.
 
Créez la table de cartographie (juste une fois) :

```kusto
.create table Devices (DeviceModel: string, FriendlyName: string) 

.ingest inline into table Devices 
    ["iPhone5,1","iPhone 5"]["iPhone3,2","iPhone 4"]["iPhone7,2","iPhone 6"]["iPhone5,2","iPhone5"]
```

Contenu des appareils maintenant:

|DeviceModel |FriendlyName 
|---|---
|iPhone5,1 |iPhone 5 
|iPhone3,2 |iPhone 4 
|iPhone7,2 |iPhone 6 
|iPhone5,2 |iPhone5 


Même astuce pour créer la table de test Source:

```kusto
.create table Source (DeviceModel: string, Count: int)

.ingest inline into table Source ["iPhone5,1",32]["iPhone3,2",432]["iPhone7,2",55]["iPhone5,2",66]
```


Joignez-vous et projetez :

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


## <a name="creating-and-using-query-time-dimension-tables"></a>Création et utilisation de tables de dimension de temps de requête

Dans de nombreux cas, on veut joindre les résultats d’une requête avec une table de dimension ad hoc qui n’est pas stockée dans la base de données. Il est possible de définir une expression dont le résultat est un tableau à portée d’une seule requête en faisant quelque chose comme ceci:

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

Voici un exemple un peu plus complexe :

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

## <a name="retrieving-the-latest-by-timestamp-records-per-identity"></a>Récupérer les derniers enregistrements (par timestamp) par identité

Supposons que vous ayez `id` un tableau qui comprend une colonne (identifiant l’entité avec laquelle chaque `timestamp` ligne est associée, comme un identifiant d’utilisateur ou un id de nœud) et une colonne (fournissant la référence de temps pour la ligne), ainsi que d’autres colonnes. Votre objectif est d’écrire une requête qui renvoie les `id` 2 derniers enregistrements pour chaque valeur de `timestamp`la colonne, où "dernier" est défini comme "ayant la valeur la plus élevée de ".

Cela peut être fait à l’aide de [l’opérateur le mieux imbriqué](topnestedoperator.md).
D’abord, nous fournissons la requête, puis nous allons l’expliquer:

```kusto
datatable(id:string, timestamp:datetime, bla:string)           // (1)
  [
  "Barak",  datetime(2015-01-01), "1",
  "Barak",  datetime(2016-01-01), "2",
  "Barak",  datetime(2017-01-20), "3",
  "Donald", datetime(2017-01-20), "4",
  "Donald", datetime(2017-01-18), "5",
  "Donald", datetime(2017-01-19), "6"
  ]
| top-nested   of id        by dummy0=max(1),                  // (2)
  top-nested 2 of timestamp by dummy1=max(timestamp),          // (3)
  top-nested   of bla       by dummy2=max(1)                   // (4)
| project-away dummy0, dummy1, dummy2                          // (5)
```

Notes
1. Le `datatable` est juste un moyen de produire des données de test à des fins de démonstration. En réalité, bien sûr, vous auriez les données ici.
2. Cette ligne signifie essentiellement " `id`retourner toutes les valeurs distinctes de ".
3. Cette ligne revient ensuite, pour les `timestamp` 2 premiers enregistrements qui maximisent `id`la colonne, les colonnes du `timestamp`niveau précédent (ici, juste) et la colonne spécifiée à ce niveau (ici, ).
4. Cette ligne ajoute les `bla` valeurs de la colonne pour chacun des enregistrements retournés par le niveau précédent. Si la table a d’autres colonnes d’intérêt, on répéterait cette ligne pour chaque colonne de ce genre.
5. Enfin, nous utilisons l’opérateur [de projet-away](projectawayoperator.md) pour `top-nested`supprimer les colonnes "extra" introduites par .

## <a name="extending-a-table-with-some-percent-of-total-calculation"></a>Extension d’un tableau avec un certain pourcentage de calcul total

Souvent, quand on a une expression tabulaire qui comprend une colonne numérique, il est désirable de présenter cette colonne à l’utilisateur en même temps que sa valeur en pourcentage du total. Supposons, par exemple, qu’il y a des requêtes dont la valeur est le tableau suivant :

|Quelques-unes|CertainsInt|
|----------|-------|
|Foo       |    100|
|Barres       |    200|

Et vous voulez afficher cette table comme:

|Quelques-unes|CertainsInt|Pct |
|----------|-------|----|
|Foo       |    100|33,3|
|Barres       |    200|66,6|

Pour ce faire, il faut calculer le `SomeInt` total (somme) de la colonne, puis diviser chaque valeur de cette colonne par le total. Il est possible de le faire pour des résultats arbitraires en donnant à ces résultats un nom en utilisant le [comme opérateur](asoperator.md):

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

## <a name="performing-aggregations-over-a-sliding-window"></a>Exécution d’agrégations au-dessus d’une fenêtre coulissante
L’exemple suivant montre comment résumer les colonnes à l’aide d’une fenêtre coulissante. Prenons, par exemple, le tableau ci-dessous, qui contient des prix des fruits par timestamps. Supposons que nous aimerions calculer le coût min, max et de somme de chaque fruit par jour, en utilisant une fenêtre coulissante de 7 jours. En d’autres termes, chaque enregistrement dans le résultat a établi des agrégats les 7 derniers jours, et le résultat contient un enregistrement par jour dans la période d’analyse.  

Table de fruits: 

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

Requête d’agrégation de fenêtre coulissante (l’explication est fournie ci-dessous les résultats de la requête) : 

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

Détails de la requête: 

La requête «s’étend» (doublons) chaque enregistrement dans la table d’entrée tout au long de 7 jours après son apparence réelle, de sorte que chaque enregistrement apparaît en fait 7 fois. Par conséquent, lors de l’exécution de l’agrégation par jour, l’agrégation comprend tous les enregistrements des 7 jours précédents.

Explication étape par étape (les chiffres se réfèrent aux chiffres dans les commentaires inlines de requête) :
1. Bin chaque enregistrement à 1d (par rapport à _start). 
2. Déterminez la fin de la plage par enregistrement - _bin 7d, à moins que ce ne soit hors de la plage _(début, fin),_ auquel cas elle est ajustée. 
3. Pour chaque enregistrement, créez une gamme de 7 jours (timestamps), à partir de la journée d’enregistrement actuel. 
4. mv-élargir le tableau, dupliquant ainsi chaque enregistrement à 7 enregistrements, 1 jour en dehors de l’autre. 
5. Effectuez la fonction d’agrégation pour chaque jour. En raison de #4, cela résume en fait les 7 _derniers_ jours. 
6. Enfin, comme les données du premier 7d sont incomplètes (il n’y a pas de période de retour 7d pour les 7 premiers jours), nous excluons les 7 premiers jours du résultat final (ils ne participent qu’à l’agrégation pour la période 2018-10-01). 

## <a name="find-preceding-event"></a>Trouver l’événement précédent
L’exemple suivant montre comment trouver un événement précédent entre 2 ensembles de données.  

*Objectif:* : Compte tenu de 2 ensembles de données, A et B, pour chaque enregistrement en B trouver son événement précédent en A (en d’autres termes, le record arg_max en A qui est encore "plus vieux" que B). Par exemple, ci-dessous se trouve la sortie prévue pour les ensembles de données de l’échantillon suivants : 

```kusto
let A = datatable(Timestamp:datetime, Id:string, EventA:string)
[
    datetime(2019-01-01 00:00:00), "x", "Ax1",
    datetime(2019-01-01 00:00:01), "x", "Ax2",
    datetime(2019-01-01 00:00:02), "y", "Ay1",
    datetime(2019-01-01 00:00:05), "y", "Ay2",
    datetime(2019-01-01 00:00:00), "z", "Az1"
];
let B = datatable(Timestamp:datetime, Id:string, EventB:string)
[
    datetime(2019-01-01 00:00:03), "x", "B",
    datetime(2019-01-01 00:00:04), "x", "B",
    datetime(2019-01-01 00:00:04), "y", "B",
    datetime(2019-01-01 00:02:00), "z", "B"
];
A; B
```

|Timestamp|Id|ÉvénementB|
|---|---|---|
|2019-01-01 00:00:00.0000000|x|Ax1 (Ax1)|
|2019-01-01 00:00:00.0000000|z|Az1 (Az1)|
|2019-01-01 00:00:01.0000000|x|Ax2|
|2019-01-01 00:00:02.0000000|y|Ay1|
|2019-01-01 00:00:05.0000000|y|Ay2|

</br>

|Timestamp|Id|EventA|
|---|---|---|
|2019-01-01 00:00:03.0000000|x|B|
|2019-01-01 00:00:04.0000000|x|B|
|2019-01-01 00:00:04.0000000|y|B|
|2019-01-01 00:02:00.0000000|z|B|

Sortie attendue : 

|Id|Timestamp|ÉvénementB|A_Timestamp|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|x|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:01.0000000|Ax2|
|y|2019-01-01 00:00:04.0000000|B|2019-01-01 00:00:02.0000000|Ay1|
|z|2019-01-01 00:02:00.0000000|B|2019-01-01 00:00:00.0000000|Az1 (Az1)|

Deux approches différentes sont suggérées pour ce problème. Vous devez tester à la fois sur votre ensemble de données spécifique pour trouver celui qui vous convient le mieux (ils peuvent fonctionner différemment sur différents ensembles de données). 

### <a name="suggestion-1"></a>Suggestion #1:
Cette suggestion sérialis les deux ensembles de données par Id et timestamp, `arg_max` puis regroupe tous les événements B avec tous leurs événements précédents A, et sélectionne le hors de tous Comme dans le groupe. 

```kusto
A
| extend A_Timestamp = Timestamp, Kind="A"
| union (B | extend B_Timestamp = Timestamp, Kind="B")
| order by Id, Timestamp asc 
| extend t = iff(Kind == "A" and (prev(Kind) != "A" or prev(Id) != Id), 1, 0)
| extend t = row_cumsum(t)
| summarize Timestamp=make_list(Timestamp), EventB=make_list(EventB), arg_max(A_Timestamp, EventA) by t, Id
| mv-expand Timestamp to typeof(datetime), EventB to typeof(string)
| where isnotempty(EventB)
| project-away t
```

### <a name="suggestion-2"></a>Suggestion #2:
Cette suggestion nécessite de définir une période max-lookback (combien «ancien» permettons-nous l’enregistrement en A à comparer à B?) et rejoint ensuite les 2 ensembles de données sur Id et cette période de retour. La jointure produit tous les candidats possibles (tous les enregistrements A qui sont plus anciens que B et dans la période de retour), et nous filtrons ensuite le plus proche de B par arg_min (TimestampB - TimestampA). Plus la période de retour est petite, on s’attend à ce que la requête soit plus performante. 

Dans l’exemple ci-dessous, la période de retour est fixée à 1m, et donc enregistrer avec Id 'z' n’a pas un événement correspondant 'A' (puisque c’est 'A' est plus vieux par 2m).

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
) on Id, _range
| where isnull(A_Timestamp) or (A_Timestamp <= B_Timestamp and B_Timestamp <= A_Timestamp + _maxLookbackPeriod)
| extend diff = coalesce(B_Timestamp - A_Timestamp, _maxLookbackPeriod*2)
| summarize arg_min(diff, *) by ID
| project Id, B_Timestamp, A_Timestamp, EventB, EventA
```

|Id|B_Timestamp|A_Timestamp|ÉvénementB|EventA|
|---|---|---|---|---|
|x|2019-01-01 00:00:03.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|x|2019-01-01 00:00:04.0000000|2019-01-01 00:00:01.0000000|B|Ax2|
|y|2019-01-01 00:00:04.0000000|2019-01-01 00:00:02.0000000|B|Ay1|
|z|2019-01-01 00:02:00.0000000||B||