---
title: Gestion des commandes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des commandes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5685833431529af22aa4d8778d121f5a4dec16d1
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744312"
---
# <a name="commands-management"></a>Gestion des commandes

## <a name="show-commands"></a>.afficher les commandes 

`.show``commands` commande retourne une table avec des commandes d’administration qui ont atteint un état final. Ces commandes sont disponibles pour interroger pendant 30 jours.

La table Commands comporte deux colonnes avec des détails de consommation de ressources de chaque commande terminée :
* TotalCpu: Le temps total de l’horloge CPU (mode utilisateur et mode Kernel) consommé par cette commande.
* ResourceUtilization: Un objet contenant toutes les informations d’utilisation des ressources liées à cette commande (y compris le TotalCpu).

La consommation de ressources étant suivie comprend des mises à jour de données, ainsi que toute requête associée à la commande admin actuelle.
Actuellement, seules quelques-unes des commandes Admin sont couvertes par la table des commandes (.ingest, .set, .append, .set-or-replace, .set-or-append), et progressivement, plus de commandes seront ajoutées à l’avenir.


* Un [administrateur de base de données ou un moniteur de base de données](../management/access-control/role-based-authorization.md) peut voir n’importe quelle commande qui a été invoquée dans leur base de données.
* D’autres utilisateurs ne peuvent voir que les commandes qui ont été invoquées par eux.

**Syntaxe**

`.show` `commands`
 
**Exemple**
 
|ClientActivitéId |CommandType |Texte |Base de données |StartedOn |LastUpdatedOn |Duration |State |RootActivityId |Utilisateur |ÉchecReason |Application |Principal |TotalCpu TotalCpu |ResourceUtilization
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--
|KD2RunCommand;a069f9e3-6062-4a0e-aa82-75a1b5e16fb4 |ExtentsMerge   |.fusionner async Opérations ...    |DB1    |2017-09-05 11:08:07.5738569    |2017-09-05 11:08:09.1051161    |00:00:01.5312592   |Completed  |b965d809-3f3e-4f44-bd2b-5e1f49ac46c5   |AAD app id-5ba8cec2-9a70-e92c98cad651  |   |Kusto.Azure.DM.Svc |aadapp-5ba8cec2-9a70-e92c98cad651  |00:00:03.5781250   |" ScannedExtentsStatistics ": 'MinDataScannedTime": null, "MaxDataScannedTime": null ', "CacheStatistics": 'Mémoire': 'Misses': 2, "Hits": 20 ', 'Disk': 'Misses': 2, "Hits": 0 ', 'MemoryPeak": 159620640, "TotalCpu": "00:00:03.5781250" 
|Ke. RunCommand;710e08ca-2cd3-4d2d-b7bd-2738d335aa50 |DataIngestPull (en anglais) |.ingest dans MyTableName ...   |TestDB |2017-09-04 16:00:37.0915452    |2017-09-04 16:04:37.2834555    |00:04:00.1919103   |Échec |a8986e9e-943f-81b0270d6fae4    |cooper@fabrikam.com    |La connexion de prise a été éliminée.   |Kusto.Explorer |aaduser...    |00:00:00   |"ScannedExtentsStatistics": "MinDataScannedTime": null, "MaxDataScannedTime": null , "CacheStatistics": 'Memory': 'Misses': 0, Hits": 0 ', 'Disk': 'Misses": 'Hits": 'Hits": 'Hits": '0 ', 'MemoryPeak": 0, "TotalCpu": "00:00:00" 
|KD2RunCommand;97db47e6-93e2-4306-8b7d-670f2c3307ff |ÉtenduesRebuild |.fusionner async Opérations ...    |DB2    |2017-09-18 13:29:38.5945531    |2017-09-18 13:29:39.9451163    |00:00:01.3505632   |Completed  |d5ebb755-d5df-4e94-b240-9accdf06c2d1   |AAD app id-5ba8cec2-9a70-e92c98cad651  |   |Kusto.Azure.DM.Svc |aadapp-5ba8cec2-9a70-e92c98cad651  |00:00:00.8906250   |" ScannedExtentsStatistics ": 'MinDataScannedTime": null, "MaxDataScannedTime": null ', "CacheStatistics": 'Mémoire": 'Misses': 0, "Hits": 1 ', 'Disk': 'Misses': 0, "Hits": 0 ', 'MemoryPeak': 88828560, "TotalCpu": "00:00:00.8906250"" 

**Exemple : extraire des données spécifiques de la colonne ResourceUtilization**

L’accès à l’une des propriétés de la colonne ResourceUtilization se fait en appelant ResourcesUtilization.xxx (où xxx est le nom de la propriété).
A noter que l’utilisation de ressources est une colonne dynamique, et donc pour travailler avec ses valeurs, il faut d’abord la convertir en un type de données spécifique (en utilisant une fonction de convertion telle que: tolong, toint, totimespan, ...).  

Par exemple :

```kusto
.show commands
| where CommandType == "TableAppend"
| where StartedOn > ago(1h)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| top 3 by MemoryPeak desc
| project StartedOn, MemoryPeak, TotalCpu, Text
```

|StartedOn |MemoryPeak (en) |TotalCpu TotalCpu |Texte
|--|--|--|--
| 2017-09-28 12:11:27.8155381   | 800396032 | 00:00:04.5312500  | .append Server_Boots <\| laisser bootStartsSourceTable - SessionStarts; ...
| 2017-09-28 11:21:26.7304547   | 750063056 | 00:00:03.8218750  | .set-or-append WebUsage <\| base de données ('CuratedDB'). WebUsage_v2 | Résumer... | Projet...
| 2017-09-28 12:16:17.4762522   | 676289120 | 00:00:00.0625000  | .set-or-append AtlasClusterEventStats with(..)) \| <Atlas_Temp(datetime(2017-09-28 12:13:28.7621737),datetime(2017-09-28 12:14:28.8168492))

## <a name="investigating-performance-issues"></a>Enquêter sur les problèmes de performance

`.show``commands` peut être utilisé afin d’étudier les problèmes de performance, car ils permettent de voir les ressources consommées par chaque commandement de contrôle.
Les exemples suivants sont des requêtes simples et utiles pour de telles enquêtes.

### <a name="querying-the-totalcpu-column"></a>Interroger la colonne TotalCpu

Top 10 des requêtes de consommation de processeur dans la dernière journée:

```kusto
.show commands
| where StartedOn > ago(1d)
| top 10 by TotalCpu
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
```

Toutes les requêtes dans les 10 dernières heures que leur TotalCpu a franchi les 3 minutes:

```kusto
.show commands
| where StartedOn > ago(10h) and TotalCpu > 3m
| project StartedOn, CommandType, ClientActivityId, TotalCpu 
| order by TotalCpu 
```

Toutes les requêtes dans les dernières 24 heures dont leur TotalCpu était au-dessus de 5 minutes, regroupées par l’utilisateur et le principal:

```kusto
.show commands  
| where StartedOn > ago(24h)
| summarize TotalCount=count(), CountAboveThreshold=countif(TotalCpu > 2m), AverageCpu = avg(TotalCpu), MaxTotalCpu=max(TotalCpu), (50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu)=percentiles(TotalCpu, 50, 95) by User, Principal
| extend PercentageAboveThreshold = strcat(substring(CountAboveThreshold * 100 / TotalCount, 0, 5), "%")
| order by CountAboveThreshold desc
| project User, Principal, CountAboveThreshold, TotalCount, PercentageAboveThreshold, MaxTotalCpu, AverageCpu, 50th_Percentile_TotalCpu, 95th_Percentile_TotalCpu
```

Timechart: CPU moyen vs 95e Percentile vs Max CPU :

```kusto
.show commands 
| where StartedOn > ago(1d) 
| summarize MaxCpu_Minutes=max(TotalCpu)/1m, 95th_Percentile_TotalCpu_Minutes=percentile(TotalCpu, 95)/1m, AverageCpu_Minutes=avg(TotalCpu)/1m by bin(StartedOn, 1m)
| render timechart
```

## <a name="querying-the-memorypeak"></a>Interroger le MemoryPeak

Top 10 des requêtes dans la dernière journée avec les valeurs les plus élevées MemoryPeak:

```kusto
.show commands
| where StartedOn > ago(1d)
| extend MemoryPeak = tolong(ResourcesUtilization.MemoryPeak)
| project StartedOn, CommandType, ClientActivityId, TotalCpu, MemoryPeak
| top 10 by MemoryPeak  
```

Timechart of Average MemoryPeak vs 95th Percentile vs Max MemoryPeak:

```kusto
.show commands 
| where StartedOn > ago(1d)
| project MemoryPeak = tolong(ResourcesUtilization.MemoryPeak), StartedOn 
| summarize Max_MemoryPeak=max(MemoryPeak), 95th_Percentile_MemoryPeak=percentile(MemoryPeak, 95), Average_MemoryPeak=avg(MemoryPeak) by bin(StartedOn, 1m)
| render timechart
```
