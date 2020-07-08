---
title: . Alter, balises d’étendue-Azure Explorateur de données
description: Cet article décrit la commande ALTER extension dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 00c4cfbb4b6415afcd68e8e41864ca4a68cc097e
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060615"
---
# <a name="alter-extent-tags"></a>. Alter, balises d’étendue

La commande s’exécute dans le contexte d’une base de données spécifique. Elle modifie les [balises d’étendue](extents-overview.md#extent-tagging) spécifiées de toutes les étendues retournées par la requête.

Les étendues et les balises à modifier sont spécifiées à l’aide d’une requête Kusto qui retourne un jeu d’enregistrements avec une colonne appelée « ExtentId ».

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md) pour toutes les tables impliquées.

> [!NOTE]
> Les partitions de données sont des **Extensions** appelées dans Kusto, et toutes les commandes utilisent « extent » ou « extents » comme synonyme.
> Pour plus d’informations sur les extensions, consultez [extents (Data partitions) Overview](extents-overview.md).

## <a name="syntax"></a>Syntax

`.alter`[ `async` ] `extent` `tags` `(` '*: Étiquette1*' [ `,` '*tag2*' `,` ... `,` ' *TagN*'] `)`  <|  *requête*

`async`(facultatif) : exécutez la commande de façon asynchrone.
   * Un ID d’opération (Guid) est retourné. 
   * L’état de l’opération peut être surveillé. Utilisez la commande [. Show Operations](operations.md#show-operations) .
   * Vous pouvez récupérer les résultats d’une exécution réussie. Utilisez la commande [. afficher les détails](operations.md#show-operation-details) de l’opération.

## <a name="restrictions"></a>Restrictions

Toutes les étendues doivent être dans la base de données de contexte et doivent appartenir à la même table.

## <a name="return-output"></a>Sortie de retour

|Paramètre de sortie |Type |Description|
|---|---|---|
|OriginalExtentId |string |Identificateur unique (GUID) de l’extension d’origine dont les balises ont été modifiées. L’étendue est supprimée dans le cadre de l’opération.|
|ResultExtentId |string |Identificateur unique (GUID) pour l’étendue de résultat qui a modifié des balises. L’extension est créée et ajoutée dans le cadre de l’opération. En cas d’échec-« échec ».|
|ResultExtentTags |string |Collection de balises avec lesquelles l’étendue du résultat est marquée, ou « null » en cas d’échec de l’opération.|
|Détails |string |Indique les détails de l’échec en cas d’échec de l’opération.|

## <a name="examples"></a>Exemples

### <a name="alter-tags"></a>Modifier les balises 

Modifier les balises de toutes les étendues de la table `MyTable` en`MyTag`

```kusto
.alter extent tags ('MyTag') <| .show table MyTable extents
```

### <a name="alter-tags-of-all-extents"></a>Modifier les balises de toutes les étendues

Modifier les balises de toutes les étendues de la table `MyTable` , balisées avec `drop-by:MyTag` `drop-by:MyNewTag` et`MyOtherNewTag`

```kusto
.alter extent tags ('drop-by:MyNewTag','MyOtherNewTag') <| .show table MyTable extents where tags has 'drop-by:MyTag'
```

## <a name="sample-output"></a>Exemple de sortie

|OriginalExtentId |ResultExtentId | ResultExtentTags | Détails
|---|---|---|---
|e133f050-a1e2-4dad-8552-1f5cf47cab69 |0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687 | Drop-by : MyNewTag MyOtherNewTag| 
|cdbeb35b-87ea-499f-b545-defbae091b57 |a90a303c-8a14-4207-8f35-d8ea94ca45be | Drop-by : MyNewTag MyOtherNewTag| 
|4fcb4598-9a31-4614-903c-0c67c286da8c |97aafea1-59ff-4312-B06B-08f42187872f | Drop-by : MyNewTag MyOtherNewTag| 
|2dfdef64-62a3-4950-a130-96b5b1083b5a |0fb7f3da-5e28-4f09-a000-e62eb41592df | Drop-by : MyNewTag MyOtherNewTag| 
