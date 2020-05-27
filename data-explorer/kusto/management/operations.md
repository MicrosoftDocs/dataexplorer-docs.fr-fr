---
title: Gestion des opérations-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Operations Management dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 40103d399feb61e994639c9447510ef90fef652d
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011463"
---
# <a name="operations-management"></a>Gestion des opérations

## <a name="show-operations"></a>. afficher les opérations

`.show``operations`la commande retourne une table avec toutes les opérations administratives, en cours d’exécution et terminées, qui ont été exécutées au cours des deux dernières semaines (qui est actuellement la configuration de la période de rétention).

**Syntaxe**

|||
|---|---| 
|`.show` `operations`              |Retourne toutes les opérations que le cluster traite ou les opérations que le cluster a traitées
|`.show``operations` *OperationId*|Retourne l’état de l’opération pour un ID spécifique 
|`.show``operations` `(` *OperationId1* `,` *OperationId2* `,` ...)|Retourne l’état des opérations pour des ID spécifiques

**Résultats**
 
|Paramètre de sortie |Type |Description
|---|---|---
|id |Chaîne |Identificateur de l’opération
|Opération |Chaîne |Alias de la commande admin
|NodeId |Chaîne |Si la commande a une exécution à distance (par exemple, DataIngestPull)-NodeId contient l’ID du nœud distant en cours d’exécution
|StartedOn |DateTime |Date/heure (au format UTC) à laquelle l’opération a démarré
|LastUpdatedOn |DateTime |Date/heure (au format UTC) de la dernière mise à jour de l’opération (peut être une étape à l’intérieur de l’opération ou une étape d’achèvement)
|Duration |DateTime |TimeSpan entre LastUpdateOn et StartedOn
|State |Chaîne |État de la commande : peut avoir les valeurs « en cours », « terminé » ou « échec »
|Statut |Chaîne |Chaîne d’aide supplémentaire qui contient des erreurs d’opérations ayant échoué
 
**Exemple**
 
|id |Opération |ID du nœud |Démarré le |Dernière mise à jour le |Duration |State |Statut 
|--|--|--|--|--|--|--|--
|3827def6-0773-4f2a-859e-c02cf395deaf |SchemaShow | |2015-01-06 08:47:01.0000000 |2015-01-06 08:47:01.0000000 |0001-01-01 00:00:00.0000000 |Effectué |
|841fafa4-076a-4cba-9300-4836da0d9c75 |DataIngestPull |Kusto. Azure. Svc_IN_1 |2015-01-06 08:47:02.0000000 |2015-01-06 08:48:19.0000000 |0001-01-01 00:01:17.0000000 |Effectué |
|e198c519-5263-4629-a158-8d68f7a1022f |OperationsShow | |2015-01-06 08:47:18.0000000 |2015-01-06 08:47:18.0000000 |0001-01-01 00:00:00.0000000 |Effectué |
|a9f287a1-f3e6-4154-ad18-b86438da0929 |ExtentsDrop | |2015-01-11 08:41:01.0000000 |0001-01-01 00:00:00.0000000 |0001-01-01 00:00:00.0000000 |InProgress |
|9edb3ecc-f4b4-4738-87e1-648eed2bd998 |DataIngestPull | |2015-01-10 14:57:41.0000000 |2015-01-10 14:57:41.0000000 |0001-01-01 00:00:00.0000000 |Failed |La collection a été modifiée. L’opération d’énumération ne peut pas s’exécuter.

## <a name="show-operation-details"></a>. afficher les détails de l’opération

Les opérations peuvent (éventuellement) conserver leurs résultats, et les résultats peuvent être récupérés lorsque l’opération est terminée, à l’aide de `.show` `operation` `details` .

> [!NOTE]
> Toutes les commandes de contrôle ne conservent pas leurs résultats. Les commandes qui effectuent, en général, le font par défaut uniquement sur les exécutions asynchrones, à l’aide du `async` mot clé. Pour plus d’informations, consultez la documentation spécifique à la commande. Par exemple, consultez [exportation de données](data-export/index.md)).
> Le schéma de sortie de la `.show` `operations` `details` commande est le même que celui retourné par l’exécution synchrone de la commande.
> La `.show` `operation` `details` commande ne peut être appelée qu’une fois l’opération terminée avec succès. Utilisez la [commande Afficher les opérations](#show-operations)) pour vérifier l’état de l’opération avant d’exécuter cette commande.

**Syntaxe**

`.show``operation` *OperationId*`details`

**Résultats**

Le résultat est différent selon le type d’opération et correspond au schéma du résultat de l’opération lorsqu’il est exécuté de façon synchrone.

**Exemples**

*OperationId* dans l’exemple, retourne à partir d’une exécution asynchrone de l’une des commandes d' [exportation de données](../management/data-export/index.md) .

```kusto 
.export 
  async 
  to csv ( 
    h@"https://storage1.blob.core.windows.net/containerName;secretKey", 
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey" 
  ) 
  <| myLogs 

```

La commande d’exportation asynchrone a retourné l’ID d’opération suivant :

|OperationId|
|---|
|56e51622-eb49-4d1a-b896-06a03178efcd|

Cet ID d’opération peut être utilisé lorsque la commande est terminée pour interroger les objets BLOB exportés. 

```kusto
.show operation 56e51622-eb49-4d1a-b896-06a03178efcd details 
```

**Résultats**

|Path|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/2_454c0f1359e24795b6529da8a0101330.csv|15|
