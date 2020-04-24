---
title: Informations diagnostiques - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les informations diagnostiques dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ae8efdf99b7ed91285e90defed7568d2a440240d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521265"
---
# <a name="diagnostic-information"></a>Informations diagnostiques

Les commandes suivantes peuvent être utilisées pour afficher des informations diagnostiques du système.

## <a name="show-cluster"></a>.afficher le cluster

```kusto
.show cluster
```

Retourne un ensemble ayant un record par nœud actuellement actif dans le cluster.  

**Résultats**

|Colonne de sortie |Type |Description 
|---|---|---|
|NodeId|String|Identifie le nœud (c’est le RoleId Azure du nœud si le cluster est déployé en Azure). |
|Adresse|String |Le critère d’évaluation interne utilisé par le cluster pour les communications inter-nœuds. 
|Nom |String |Nom interne du nœud (cela inclut le nom de la machine, le nom du processus et l’ID du processus). 
|StartTime |DateTime |La date exacte /heure (dans UTC) que l’instantanéie actuelle Kusto dans le nœud a commencé. Cela peut être utilisé pour détecter si le nœud (ou Kusto en cours d’exécution sur le nœud) a été redémarré récemment. 
|IsAdmin |Boolean |Si ce nœud est actuellement le «leader» du cluster. 
|MachineTotalMemory  |Int64 |La quantité de RAM que le nœud a. 
|MachineAvailableMemory  |Int64 |La quantité de RAM qui est actuellement "disponible pour une utilisation" sur le nœud. 
|ProcessorCount  |Int32 |La quantité de processeurs sur le nœud. 
|EnvironnementDéscription  |string |Informations supplémentaires sur l’environnement du nœud (p. ex. Upgrade/Fault Domains), sérialisées sous le nom de JSON. 

**Exemple**

```kusto
.show cluster
```

NodeId|Adresse|Nom|StartTime|IsAdmin|MachineTotalMemory|MachineAvailableMemory|ProcessorCount|EnvironnementDéscription
---|---|---|---|---|---|---|---|---
Kusto.Azure.Svc_IN_1|net.tcp://100.112.150.30:23107/|Kusto.Azure.Svc_IN_4/RD000D3AB1E9BD/WaWorkerHost/3820|2016-01-15 02:00:22.6522152|True|274877435904|247797796864|16|"UpdateDomain":0, "FaultDomain":0
Kusto.Azure.Svc_IN_3|net.tcp://100.112.154.34:23107/|Kusto.Azure.Svc_IN_3/RD000D3AB1E062/WaWorkerHost/2760|2016-01-15 05:52:52.1434683|False|274877435904|258740346880|16|"UpdateDomain":1, "FaultDomain":1
Kusto.Azure.Svc_IN_2|net.tcp://100.112.128.40:23107/|Kusto.Azure.Svc_IN_2/RD000D3AB1E054/WaWorkerHost/3776|2016-01-15 07:17:18.0699790|False|274877435904|244232339456|16|"UpdateDomain":2, "FaultDomain":2
Kusto.Azure.Svc_IN_0|net.tcp://100.112.138.15:23107/|Kusto.Azure.Svc_IN_0/RD000D3AB0D6C6/WaWorkerHost/3208|2016-01-15 09:46:36.9865016|False|274877435904|238414581760|16|"UpdateDomain":3, "FaultDomain":3


## <a name="show-diagnostics"></a>.afficher les diagnostics

```kusto
.show diagnostics
```

Retourne une information sur l’état de santé de cluster de Kusto.
 
**Retourne**

|Paramètre de sortie |Type |Description|
|-----------------|-----|-----------| 
|IsHealthy (en)|Boolean|Que le cluster soit jugé sain ou non.
|IsScaleOutRequired|Boolean|Si le cluster doit être augmenté en taille (ajouter plus de nœuds informatiques). 
|MachinesTotal|Int64|Le nombre de machines dans le cluster.
|MachinesOffline|Int64|Le nombre de machines qui sont actuellement hors ligne (ne répondent pas).
|NodeLastRestartedOn|DateTime|La dernière fois que l’un des nœuds de l’amas a été redémarré.
|AdminLastElectedOn|DateTime|La dernière fois que la propriété du rôle d’administrateur de cluster a changé.
|MemoryLoadFactor (memoryLoadFactor)|Double|La quantité de données détenues par le cluster par rapport à sa capacité (qui est de 100,0)
|ExtentsTotal|Int64|Le nombre total d’étendues de données que le cluster a actuellement dans toutes les bases de données et toutes les tables.
|Réservé|Int64|
|Réservé|Int64|
|InstancesTargetBasedOnDataCapacity|Int64| Nombre d’instances nécessaires pour ramener le ClusterDataCapacityFactor en dessous de 80 (valable uniquement lorsque toutes les machines sont classées de la même façon).
|TotalOriginalDataSize TotalOriginalDataSize TotalOriginalDataSize TotalOri|Int64|Taille totale des données ingérées à l’origine
|TotalExtentSize|Int64|Taille totale des données stockées (après compression et indexation)
|IngestionsLoadFactor|Double|Le pourcentage d’utilisation de la capacité d’ingestion de cluster (peut être vu en utilisant la commande de capacité de .show)
|IngestionsInProgresse|Int64|Nombre d’opérations d’ingestion en cours.
|IngestionsSuccessRate|Double|Le pourcentage d’opérations d’ingestion qui s’est terminée avec succès au cours des 10 dernières minutes.
|FusionsInProgress|Int64|Nombre d’étendues d’exploitation de fusion en cours.
|BuildVersion|String|La version logicielle Kusto déployée sur le cluster.
|BuildTime (en)|DateTime|Le temps de construire de cette version logicielle Kusto.
|ClusterDataCapacityFactor|Double|Le pourcentage de l’utilisation de la capacité de données de cluster. Il est calculé comme SUM (Extent Size Data) / SUM (SSD Cache Size).
|IsDataWarmingRequired|Boolean|Interne : Si les requêtes de réchauffement du cluster doivent-elles être exécutées (afin d’apporter des données au cache SSD local). 
|DataWarmingLastRunOn|DateTime|La dernière fois que des données .warm ont été exécutées sur le cluster
|FusionsSuccessRate|Double|Le pourcentage d’opérations de fusion qui se sont terminées avec succès au cours des 10 dernières minutes.
|PasHealthyReason|String|Chaîne spécifiant la raison pour laquelle le cluster n’est pas en bonne santé 
|IsAttentionRequired|Boolean|La question de savoir si le cluster nécessite l’attention de l’équipe d’opération
|AttentionRequiredReason|String|Chaîne spécifiant la raison de l’amas nécessitant une attention
|ProductVersion|String|Chaîne avec informations sur le produit (branche, version, etc.)
|Échec Des opératations|Int64|Nombre d’opérations d’ingestion ratées au-delà de 10 minutes
|Échec Desoperations|Int64|Nombre d’opérations de fusion ratées au-delà d’une heure
|MaxExtentsInSingleTable|Int64|Nombre maximum d’étendues dans le tableau (TableWithMaxExtents)
|TableWithMaxExtents|String|Tableau avec le nombre maximum d’étendues (MaxExtentsInSingleTable)
|WarmExtentSize|Double|Taille totale des étendues dans le cache chaud
|NombreOfDatabases|Int32|Nombre de bases de données dans le cluster

## <a name="show-capacity"></a>.montrer la capacité 

```kusto
.show capacity
```

Retourne un calcul pour une capacité de cluster estimée pour chaque ressource. 
 
**Résultats**

|Paramètre de sortie |Type |Description 
|---|---|---
|Ressource |String |Nom de la ressource. 
|Total |Int64 |Montant des ressources totales de type « ressource » qui sont disponibles (p. ex. quantité d’ingéres simultanés) 
|Consommé |Int64 |La quantité de ressources de type «ressource» consommées en ce moment 
|Restant |Int64 |La quantité de ressources restantes de type «ressource» 
 
**Exemple**

|Ressource |Total |Consommé |Restant 
|---|---|---|---
|ingestions |576 |1 |575 

## <a name="show-operations"></a>.afficher les opérations 

Retourne une table avec toutes les opérations administratives depuis l’élection du nouveau nœud Admin. 

|||
|---|---| 
|`.show` `operations`              |Renvoie toutes les opérations traitées par le cluster ou le traitement 
|`.show``operations` *OpérationId*|Retourne l’état de l’opération pour une pièce d’identité spécifique 
|`.show``operations` `,` *OperationId2* `,` *OperationId1* OpérationId1 OperationId2 ...) `(`|Retours de l’état des opérations pour des DIU spécifiques

**Résultats**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|Id |String |Opération Identifier. 
|Opération |String |Admin commande alias 
|NodeId |String |Si la commande a une exécution à distance (par exemple DataIngestPull) - NodeId contiendra l’id du nœud à distance exécutant 
|StartedOn |DateTime |Date/heure (dans UTC) lorsque l’opération a commencé 
|LastUpdatedOn |DateTime |Date/heure (dans UTC) lorsque l’opération a été mise à jour pour la dernière fois (peut être soit un pas à l’intérieur de l’opération, soit une étape d’achèvement) 
|Duration |DateTime |TimeSpan entre LastUpdateOn et StartedOn 
|State |String |État de commandement : peut avoir des valeurs de « InProgress », « Complété » ou « Échec » 
|Statut |String |Chaîne d’aide supplémentaire qui détient soit des erreurs pour les opérations ratées 
 
**Exemple**
 
|Id |Opération |ID du nœud |Commencé sur |Dernière mise à jour sur |Duration |State |Statut 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow (SchemaShow) | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |Completed | 
|841fafa4-076a-4cba-9300-4836da0d9c75 |DataIngestPull (en anglais) |Kusto.Azure.Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |Completed | 
|e198c519-5263-4629-a158-8d68f7a1022f |OperationsShow (en) | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |Completed | 
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop (en) | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress | 
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull (en anglais) | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |Échec |La collecte a été modifiée; l’opération d’énumération ne peut pas s’exécuter. 