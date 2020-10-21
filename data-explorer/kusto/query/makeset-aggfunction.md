---
title: make_set () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_set () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: 8f5494c9d54d2950ba82da8de0b0094b2d17f798
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246412"
---
# <a name="make_set-aggregation-function"></a>make_set () (fonction d’agrégation)

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes prises par *Expr* dans le groupe.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

`summarize``make_set(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression pour le calcul de l’agrégation.
* *MaxSize* est une limite d’entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *1048576*). La valeur MaxSize ne peut pas dépasser 1048576.

> [!NOTE]
> Une variante héritée et obsolète de cette fonction : `makeset()` a une limite par défaut de *MaxSize* = 128.

## <a name="returns"></a>Retours

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes prises par *Expr* dans le groupe.
L’ordre de tri du tableau n’est pas défini.

> [!TIP]
> Pour compter uniquement des valeurs distinctes, utilisez [DCount ()](dcount-aggfunction.md)

## <a name="example"></a>Exemple

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Tableau de la requête Kusto synthétiser les pays par continent dans Azure Explorateur de données":::

## <a name="see-also"></a>Voir aussi

* Utilisez [`mv-expand`](./mvexpandoperator.md) l’opérateur pour la fonction opposée.
* [`make_set_if`](./makesetif-aggfunction.md) est semblable à `make_set` , sauf qu’il accepte également un prédicat.
