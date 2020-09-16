---
title: Informations de diagnostic-Azure Explorateur de données
description: Cet article décrit les informations de diagnostic dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 60d25403b230be9ef625a6d52d1fdb159f9fc4e3
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680658"
---
# <a name="diagnostic-information"></a>Informations de diagnostic

Ces commandes peuvent être utilisées pour afficher des informations de diagnostic du système.

* [. afficher le cluster](#show-cluster)
* [. afficher les Diagnostics](#show-diagnostics)
* [. afficher la capacité](#show-capacity)
* [. afficher les opérations](#show-operations)

## <a name="show-cluster"></a>. afficher le cluster

```kusto
.show cluster
```

Retourne un jeu qui contient un enregistrement pour chaque nœud actuellement actif dans le cluster.  

**Résultats** 

|Colonne de sortie |Type |Description
|---|---|---|
|NodeId|String|Identifie le nœud. L’ID de nœud est le RoleId Azure du nœud, si le cluster est déployé dans Azure
|Adresse|String |Point de terminaison interne utilisé par le cluster pour les communications entre les nœuds
|Nom |String |Nom interne du nœud. Le nom comprend le nom de l’ordinateur, le nom du processus et l’ID du processus.
|StartTime |DateTime |Date/heure (au format UTC) de début de l’instanciation Kusto actuelle dans le nœud. Cette valeur peut être utilisée pour détecter si le nœud (ou Kusto en cours d’exécution sur le nœud) a été redémarré récemment.
|IsAdmin |Booléen |Si ce nœud est actuellement le « responsable » du cluster 
|MachineTotalMemory  |Int64 |Quantité de RAM du nœud.
|MachineAvailableMemory  |Int64 |Quantité de mémoire vive (RAM) actuellement disponible pour une utilisation sur le nœud.
|ProcessorCount  |Int32 |Nombre de processeurs sur le nœud.
|EnvironmentDescription  |string |Informations supplémentaires sur l’environnement du nœud sérialisé au format JSON. Par exemple, en tant que domaines de mise à niveau/d’erreur

**Exemple**

```kusto
.show cluster
```

ID|Adresse|Nom|StartTime|IsAdmin|MachineTotalMemory|MachineAvailableMemory|ProcessorCount|EnvironmentDescription
---|---|---|---|---|---|---|---|---
Kusto. Azure. Svc_IN_1|NET. TCP://100.112.150.30:23107/|Kusto. Azure. Svc_IN_4/RD000D3AB1E9BD/WaWorkerHost/3820|2016-01-15 02:00:22.6522152|True|274877435904|247797796864|16|{"UpdateDomain" : 0, "FaultDomain" : 0}
Kusto. Azure. Svc_IN_3|NET. TCP://100.112.154.34:23107/|Kusto. Azure. Svc_IN_3/RD000D3AB1E062/WaWorkerHost/2760|2016-01-15 05:52:52.1434683|False|274877435904|258740346880|16|{"UpdateDomain" : 1, "FaultDomain" : 1}
Kusto. Azure. Svc_IN_2|NET. TCP://100.112.128.40:23107/|Kusto. Azure. Svc_IN_2/RD000D3AB1E054/WaWorkerHost/3776|2016-01-15 07:17:18.0699790|False|274877435904|244232339456|16|{"UpdateDomain" : 2, "FaultDomain" : 2}
Kusto. Azure. Svc_IN_0|NET. TCP://100.112.138.15:23107/|Kusto. Azure. Svc_IN_0/RD000D3AB0D6C6/WaWorkerHost/3208|2016-01-15 09:46:36.9865016|False|274877435904|238414581760|16|{"UpdateDomain" : 3, "FaultDomain" : 3}


## <a name="show-diagnostics"></a>. afficher les Diagnostics

```kusto
.show diagnostics
```

Retourne des informations sur l’état d’intégrité du cluster Kusto.
 
**Retourne**

|Paramètre de sortie |Type |Description|
|-----------------|-----|-----------| 
|IsHealthy|Booléen|Si le cluster est sain ou non
|IsScaleOutRequired|Booléen|Si la taille du cluster doit être augmentée en ajoutant des nœuds de calcul supplémentaires
|MachinesTotal|Int64|Nombre d’ordinateurs dans le cluster
|MachinesOffline|Int64|Nombre d’ordinateurs actuellement hors connexion
|NodeLastRestartedOn|DateTime|Date/heure de la dernière redémarrage de tout nœud du cluster
|AdminLastElectedOn|DateTime|La dernière propriété de date/heure du rôle d’administrateur de cluster a changé
|MemoryLoadFactor|Double|Quantité de données détenues par le cluster, par rapport à sa capacité maximale de 100,0
|ExtentsTotal|Int64|Nombre total d’étendues de données actuellement présentes dans le cluster, sur toutes les bases de données et toutes les tables
|Réservé|Int64|
|Réservé|Int64|
|InstancesTargetBasedOnDataCapacity|Int64|Nombre d’instances nécessaires pour amener le ClusterDataCapacityFactor inférieur à 80. Cette valeur est valide uniquement lorsque tous les ordinateurs sont dimensionnés de la même façon
|TotalOriginalDataSize|Int64|Taille totale des données ingérées à l’origine en octets
|TotalExtentSize|Int64|Taille totale des données stockées, après la compression et l’indexation en octets
|IngestionsLoadFactor|Double|Pourcentage de la capacité d’ingestion du cluster qui a été utilisé. La capacité maximale peut être affichée à l’aide de la `.show capacity` commande
|IngestionsInProgress|Int64|Nombre d’opérations d’ingestion en cours
|IngestionsSuccessRate|Double|Le pourcentage d’opérations d’ingestion qui se sont terminées avec succès au cours des 10 dernières minutes
|MergesInProgress|Int64|Nombre d’opérations de fusion d’étendues en cours
|BuildVersion|String|La version du logiciel Kusto déployée sur le cluster
|BuildTime|DateTime|Date/heure de la version de build du logiciel Kusto.
|ClusterDataCapacityFactor|Double|Pourcentage de la capacité de données du cluster utilisé. Le pourcentage est calculé comme étant SUM (Extent Size Data)/SUM (taille du cache SSD).
|IsDataWarmingRequired|Booléen|Interne : si les requêtes de réchauffement du cluster doivent être exécutées, pour importer les données dans le cache SSD local 
|DataWarmingLastRunOn|DateTime|Date/heure de la dernière exécution des données. chaude sur le cluster
|MergesSuccessRate|Double|Pourcentage d’opérations de fusion qui se sont terminées avec succès au cours des 10 dernières minutes.
|NotHealthyReason|String|Spécifie la raison pour laquelle le cluster n’est pas sain 
|IsAttentionRequired|Booléen|Si le cluster nécessite une attention de l’équipe chargée des opérations
|AttentionRequiredReason|String|Spécifie la raison pour laquelle le cluster nécessite une attention
|ProductVersion|String|Spécifie les informations sur le produit (branche, version, etc.)
|FailedIngestOperations|Int64|Nombre d’opérations d’ingestion ayant échoué au cours des 10 dernières minutes
|FailedMergeOperations|Int64|Nombre d’opérations de fusion ayant échoué au cours de la précédente 1 heure
|MaxExtentsInSingleTable|Int64|Nombre maximal d’étendues dans la table (TableWithMaxExtents)
|TableWithMaxExtents|String|Table avec le nombre maximal d’extensions (MaxExtentsInSingleTable)
|WarmExtentSize|Double|Taille totale, en octets, des étendues dans le cache actif
|NumberOfDatabases|Int32|Nombre de bases de données dans le cluster

## <a name="show-capacity"></a>. afficher la capacité

```kusto
.show capacity
```

Retourne les résultats d’un calcul pour une capacité estimée de cluster pour chaque ressource.
 
**Résultats**

|Paramètre de sortie |Type |Description 
|---|---|---
|Ressource |String |nom de la ressource. 
|Total |Int64 |Quantité totale de ressources, de type « ressource », qui sont disponibles. Par exemple, le nombre d’ingestions simultanées
|Consommé |Int64 |Quantité de ressources de type « ressource » consommée pour le moment
|Restant |Int64 |Quantité de ressources restantes de type « ressource »
 
**Exemple**

|Ressource |Total |Consommé |Restant
|---|---|---|---
|ingestions |576 |1 |575

## <a name="show-operations"></a>. afficher les opérations

Cette commande retourne une table qui contient toutes les opérations d’administration depuis l’élection du nouveau nœud d’administration.

|Option de syntaxe |Description|
|---|---| 
|`.show` `operations`              |Retourne toutes les opérations que le cluster est en cours de traitement ou a traitées.
|`.show``operations` *OperationId*|Retourne l’état de l’opération pour un ID spécifique
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` ...)|Retourne l’état des opérations pour des ID spécifiques

**Résultats**
 
|Paramètre de sortie |Type |Description
|---|---|---
|id |String |Identificateur de l’opération
|Opération |String |Alias de la commande admin
|NodeId |String |Si la commande exécute un événement à distance, par exemple DataIngestPull. L’ID de nœud contient l’ID du nœud distant en cours d’exécution.
|StartedOn |DateTime |Date/heure (au format UTC) à laquelle l’opération a démarré 
|LastUpdatedOn |DateTime |Date/heure (au format UTC) de la dernière mise à jour de l’opération. L’opération peut être une étape à l’intérieur de l’opération ou une étape d’achèvement
|Duration |DateTime |Intervalle de temps entre LastUpdateOn et StartedOn
|État |String |État de la commande, avec les valeurs « en cours », « terminé » ou « échec »
|Statut |String |Chaîne d’aide supplémentaire qui contient les erreurs pour les opérations ayant échoué
 
**Exemple**
 
|id |Opération |ID du nœud |Démarré le |Dernière mise à jour le |Duration |État |Statut 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |Effectué | 
|841fafa4-076a-4cba-9300-4836da0d9c75 |DataIngestPull |Kusto. Azure. Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |Effectué | 
|e198c519-5263-4629-a158-8d68f7a1022f |OperationsShow | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |Effectué |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress |
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |Échec |La collection a été modifiée. L’opération d’énumération peut ne pas s’exécuter |
