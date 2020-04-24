---
title: Gestion des opérations - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gestion des opérations dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 01f30a8d391948d5466ef76b2951d55aa6084e15
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520687"
---
# <a name="operations-management"></a>Gestion des opérations

## <a name="show-operations"></a>.afficher les opérations

`.show``operations` commande renvoie un tableau avec toutes les opérations administratives, à la fois en cours d’exécution et terminée, qui ont été exécutés au cours des deux dernières semaines (qui est actuellement la configuration de la période de conservation).

**Syntaxe**

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

## <a name="show-operation-details"></a>.afficher les détails de l’opération

Les opérations peuvent (d’option) persister leurs résultats, et celles-ci peuvent être récupérées lorsque l’opération est terminée à l’aide de la `.show` `operation``details` 

**Remarques :**

* Toutes les commandes de contrôle ne persistent pas leurs résultats, et celles qui le font `async` habituellement par défaut sur des exécutions asynchrones seulement (en utilisant le mot clé). S’il vous plaît rechercher la documentation pour la commande spécifique et vérifier si elle le fait (voir, par exemple [l’exportation de données](data-export/index.md)). 
* Le schéma de `.show` `operations` `details` sortie de la commande est le même schéma retourné de l’exécution synchrone de la commande. 
* `.show` `operation` La `details` commande ne peut être invoquée qu’après la fin de l’opération. Utilisez la [commande des opérations d’exposition](#show-operations)) pour vérifier l’état de l’opération avant d’invoquer cette commande. 

**Syntaxe**

`.show``operation` *OpérationId*`details`

**Résultats**

Le résultat est différent par type d’opération et correspond au schéma du résultat de l’opération, lorsqu’il est exécuté de façon synchronisée. 

**Exemples**

*L’OpérationId* dans cet exemple est celle qui est revenue d’une exécution asynchrone de l’une des commandes [d’exportation](../management/data-export/index.md) de données.

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 

```
Le commandement d’exportation async a retourné l’id d’opération suivant :

|OpérationId|
|---|
|56e51622-eb49-4d1a-b896-06a03178efcd|

Cette pièce d’identité d’opération peut être utilisée lorsque la commande est terminée pour interroger les blobs exportés, comme suit : 

```
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

**Résultats**

|Path|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|