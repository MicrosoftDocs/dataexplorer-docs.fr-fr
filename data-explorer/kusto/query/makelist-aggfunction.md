---
title: make_list () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_list () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: fed2616f5fbd32b1c80f936d5689261467a9dc5e
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224831"
---
# <a name="make_list-aggregation-function"></a>make_list () (fonction d’agrégation)

Retourne un tableau (JSON) `dynamic` de toutes les valeurs de *Expr* dans le groupe.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

**Syntaxe**

`summarize``make_list(` *Expr* [ `,` *MaxSize*]`)`

**Arguments**

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation.
* *MaxSize* est une limite d’entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *1048576*). La valeur MaxSize ne peut pas dépasser 1048576.

> [!NOTE]
> Une variante héritée et obsolète de cette fonction : `makelist()` a une limite par défaut de *MaxSize* = 128.

**Retourne**

Retourne un tableau (JSON) `dynamic` de toutes les valeurs de *Expr* dans le groupe.
Si l’entrée de l' `summarize` opérateur n’est pas triée, l’ordre des éléments dans le tableau résultant n’est pas défini.
Si l’entrée de l' `summarize` opérateur est triée, l’ordre des éléments dans le tableau résultant suit celui de l’entrée.

> [!TIP]
> Utilisez l' [`mv-apply`](./mv-applyoperator.md) opérateur pour créer une liste ordonnée à l’aide d’une clé. Consultez les exemples [ici](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).

**Voir aussi**

[`make_list_if`](./makelistif-aggfunction.md)est semblable à `make_list` , sauf qu’il accepte également un prédicat.