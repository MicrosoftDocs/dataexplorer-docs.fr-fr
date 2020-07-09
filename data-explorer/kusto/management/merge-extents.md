---
title: . extensions de fusion-Azure Explorateur de données
description: Cet article décrit la commande fusionner les extensions dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/02/2020
ms.openlocfilehash: 6b633358713b0ff48d14fc9c5ca2a907bad0afab
ms.sourcegitcommit: d6f35df833d5b4f2829a8924fffac1d0b49ce1c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060659"
---
# <a name="merge-extents"></a>.merge extents

Cette commande fusionne les étendues indiquées par leurs ID dans la table spécifiée. 

> [!NOTE]
> Les partitions de données sont des **Extensions** appelées dans Kusto, et toutes les commandes utilisent « extent » ou « extents » comme synonyme.
> Pour plus d’informations sur les extensions, consultez [extents (Data partitions) Overview](extents-overview.md).

## <a name="syntax"></a>Syntaxe

`.merge``[async | dryrun]` *TableName* `(` *guid1* [ `,` *GUID2* ...] `)``[with(rebuild=true)]`

Il existe deux types d’opérations de fusion :
* *Fusion* qui reconstruit des index
* *Rebuild* qui redonne complètement les données

Trois options sont disponibles :
* `async`: Exécutez la commande de façon asynchrone. 
    * Un ID d’opération (Guid) est retourné.
    * L’état de l’opération peut être surveillé. Utilisez l’ID d’opération avec la `.show operations <operation ID>` commande.
* `dryrun`: L’opération répertorie uniquement les étendues qui doivent être fusionnées, mais ne les fusionne pas réellement.
* `with(rebuild=true)`: Les étendues sont reconstruites et les données sont ingérées au lieu d’être fusionnées. Les index seront reconstruits.

## <a name="return-output"></a>Sortie de retour

Paramètre de sortie |Type |Description
---|---|---
OriginalExtentId |string |Identificateur unique (GUID) de l’étendue d’origine dans la table source, qui a été fusionné dans l’étendue cible.
ResultExtentId |string |Identificateur unique (GUID) pour l’étendue qui a été créée à partir des étendues sources. En cas d’échec : « échec » ou « abandonné ».
Duration |intervalle de temps |Durée nécessaire pour effectuer l’opération de fusion.

## <a name="examples"></a>Exemples

### <a name="rebuild-two-specific-extents-in-mytable-asynchronously"></a>Reconstruisez deux extensions spécifiques dans `MyTable` , de manière asynchrone.

```kusto
.merge async MyTable (e133f050-a1e2-4dad-8552-1f5cf47cab69, 0d96ab2d-9dd2-4d2c-a45e-b24c65aa6687) with(rebuild=true)
```

### <a name="merge-two-specific-extents-in-mytable-synchronously"></a>Fusionnez deux extensions spécifiques dans `MyTable` , de façon synchrone.

```kusto
.merge MyTable (12345050-a1e2-4dad-8552-1f5cf47cab69, 98765b2d-9dd2-4d2c-a45e-b24c65aa6687)
```

## <a name="notes"></a>Notes

* En général, les `.merge` commandes ne doivent pas être exécutées manuellement. Les commandes sont exécutées en continu et automatiquement en arrière-plan du cluster, en fonction des [stratégies de fusion](mergepolicy.md) pour les tables et les bases de données.  
  * Pour plus d’informations sur les critères de fusion de plusieurs extensions en une seule, consultez [stratégie de fusion](mergepolicy.md).
* `.merge`les opérations ont un état final possible de `Abandoned` , qui peut être observé lors de `.show operations` l’exécution avec l’ID d’opération. Cet État suggère que les étendues sources n’ont pas été fusionnées, car certaines étendues sources n’existent plus dans la table source. L' `Abandoned` État se produit dans les cas suivants :
   * Les étendues sources ont été supprimées dans le cadre de la rétention de la table.
   * Les étendues sources ont été déplacées vers une autre table.
   * La table source a été entièrement supprimée ou renommée.
