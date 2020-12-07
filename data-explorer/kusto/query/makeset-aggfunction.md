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
ms.openlocfilehash: 6e57f22dae1abef3838ed6f7065759c4a203e389
ms.sourcegitcommit: 1bdbfdc04c4eac405f3931059bbeee2dedd87004
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2020
ms.locfileid: "96303304"
---
# <a name="make_set-aggregation-function"></a>make_set () (fonction d’agrégation)

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes prises par *Expr* dans le groupe.

* Peut être utilisé uniquement dans un contexte d’agrégation à l’intérieur de l’opérateur [summarize](summarizeoperator.md).

## <a name="syntax"></a>Syntaxe

`summarize``make_set(` *Expr* [ `,` *MaxSize*]`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression pour le calcul de l’agrégation.
* *MaxSize* est une limite d’entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *1048576*). La valeur MaxSize ne peut pas dépasser 1048576.

> [!NOTE]
> `makeset()` est une version héritée et obsolète de la fonction `make_set` . La version héritée a une limite par défaut de *MaxSize* = 128.

## <a name="returns"></a>Retours

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes prises par *Expr* dans le groupe.
L’ordre de tri du tableau n’est pas défini.

> [!TIP]
> Pour compter uniquement des valeurs distinctes, utilisez [DCount ()](dcount-aggfunction.md)

## <a name="example"></a> Exemple

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Tableau de la requête Kusto synthétiser les pays par continent dans Azure Explorateur de données":::

## <a name="see-also"></a>Voir aussi

* Utilisez [`mv-expand`](./mvexpandoperator.md) l’opérateur pour la fonction opposée.
* [`make_set_if`](./makesetif-aggfunction.md) est semblable à `make_set` , sauf qu’il accepte également un prédicat.
