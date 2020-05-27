---
title: Gestion des commandes-Azure Explorateur de données
description: Cet article décrit la gestion des commandes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e6a31e2c79ae658cceecfdad0ce307e2522bb55b
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011412"
---
# <a name="commands-management"></a>Gestion des commandes

## <a name="show-commands"></a>. afficher les commandes 

`.show commands`retourne une table avec des commandes d’administration qui ont atteint un état final. Ces commandes peuvent être interrogées pendant 30 jours.

La table commandes contient deux colonnes avec des détails sur la consommation des ressources de chaque commande terminée.

* TotalCpu : durée totale d’horloge de l’UC (mode utilisateur + mode noyau) consommée par cette commande.
* ResourceUtilization : contient toutes les informations relatives à l’utilisation des ressources associées à cette commande, y compris les TotalCpu.

La consommation des ressources suivie comprend les mises à jour des données et toute requête associée à la commande d’administration actuelle.
Actuellement, seules certaines des commandes d’administration sont couvertes par la table de commandes ( `.ingest` , `.set` , `.append` , `.set-or-replace` , `.set-or-append` ). Progressivement, davantage de commandes seront ajoutées à la table de commandes.

* Un [administrateur de base de données ou une analyse de base de données](../management/access-control/role-based-authorization.md) peut voir toutes les commandes qui ont été appelées dans leur base de données.
* Les autres utilisateurs peuvent uniquement voir les commandes qui ont été appelées par eux-mêmes.

**Syntaxe**

`.show` `commands`
 
**Exemple**
 
|ClientActivityId |CommandType |Texte |Base de données |StartedOn |LastUpdatedOn |Duration |State |RootActivityId |Utilisateur |FailureReason |Application |Principal |TotalCpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |ExtentsMerge   |. fusionner les opérations asynchrones...    |DB1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |Effectué  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |ID d’application AAD = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM. svc |aadapp = 5ba8cec2-9a70-e92c98cad651  |00:00:03.5781250   |{"ScannedExtentsStatistics" : {"MinDataScannedTime" : null, "MaxDataScannedTime" : null}, "CacheStatistics" : {Memory " : {" échecs " : 2," hits " : 20}," Disk " : {" échecs " : 2," hits " : 0}}," MemoryPeak " : 159620640," TotalCpu " :" 00:00:03.5781250 "} 
|KE. RunCommand 710e08ca-2cd3-4d2d-b7bd-2738d335aa50    |DataIngestPull |. réception dans MyTableName...   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |Failed |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |La connexion de socket a été supprimée.   |Kusto.Explorer |aaduser =...    |00:00:00   |{"ScannedExtentsStatistics" : {"MinDataScannedTime" : null, "MaxDataScannedTime" : null}, "CacheStatistics" : {"Memory" : {"échecs" : 0, hits " : 0}," Disk " : {" échecs " : 0," hits " : 0}}," MemoryPeak " : 0," TotalCpu " :" 00:00:00 "} 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |ExtentsRebuild |. fusionner les opérations asynchrones...    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |Effectué  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |ID d’application AAD = 5ba8cec2-9a70-e92c98cad651  |   |Kusto. Azure. DM. svc |aadapp = 5ba8cec2-9a70-e92c98cad651  |00:00:00.8906250   |{"ScannedExtentsStatistics" : {"MinDataScannedTime" : null, "MaxDataScannedTime" : null}, "CacheStatistics" : {Memory " : {" échecs " : 0," hits " : 1}," Disk " : {" échecs " : 0," hits " : 0}}," MemoryPeak " : 88828560," TotalCpu " :" 00:00:00.8906250 "} 

**Exemple : extraction de données spécifiques de la colonne ResourceUtilization**

L’accès à l’une des propriétés dans la colonne ResourceUtilization s’effectue en appelant ResourcesUtilization.xxx (où xxx est le nom de la propriété).
> [!NOTE] 
> `ResourceUtilization`est une colonne dynamique. Pour utiliser ses valeurs, vous devez d’abord le convertir en un type de données spécifique. Utilisez une fonction de conversion comme `tolong` , `toint` , `totimespan` .  

Par exemple,

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|StartedOn |MemoryPeak |TotalCpu |Texte
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500 |. Append Server_Boots <\| laisser bootStartsSourceTable = SessionStarts ;...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750 |. set-or-ajouter WebUsage <\| base de données (« CuratedDB »). WebUsage_v2 | résumer... | projet...
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000 |. set-or-Append AtlasClusterEventStats avec (...) <\| Atlas_Temp (DateTime (2017-09-28 12:13:28.7621737), DateTime (2017-09-28 12:14:28.8168492))

## <a name="investigating-performance-issues"></a>Examen des problèmes de performances

`.show``commands`peut être utilisé pour examiner les problèmes de performances, car ils affichent les ressources consommées par chaque commande de contrôle.

Les exemples suivants sont des requêtes simples et utiles pour ces investigations.

### <a name="query-the-totalcpu-column"></a>Interroger la colonne TotalCpu

Les 10 principales requêtes consommatrices de ressources au cours du dernier jour.

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

Toutes les requêtes au cours des 10 dernières heures dont TotalCpu a dépassé 3 minutes.

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

Toutes les requêtes au cours des dernières 24 heures dont TotalCpu a passé 5 minutes, regroupées par utilisateur et principal.

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

Graphique temporel : UC moyenne contre 95e centile et processeur maximal.

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="query-the-memorypeak"></a>Interroger le MemoryPeak

10 requêtes les plus importantes au cours du dernier jour avec les valeurs MemoryPeak les plus élevées.

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

Graphique temporel de moyenne MemoryPeak vs 95e centile et MemoryPeak max.

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
