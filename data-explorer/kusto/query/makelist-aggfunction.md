---
title: make_list() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_list () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e46dbfac7b8c819f66d469c160452ec4dddfb769
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512748"
---
# <a name="make_list-aggregation-function"></a>make_list)) (fonction d’agrégation)

Retourne un tableau (JSON) `dynamic` de toutes les valeurs de *Expr* dans le groupe.

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize``make_list(` *Expr* `,` [ *MaxSize*]`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation.
* *MaxSize* est une limite d’intégrisation facultative sur le nombre maximum d’éléments retournés (par défaut est *1048576*). La valeur MaxSize ne peut excéder 1048576.

> [!NOTE]
> Une variante héritée et `makelist()` obsolète de cette fonction : a une limite par défaut de *MaxSize* 128.

**Retourne**

Retourne un tableau (JSON) `dynamic` de toutes les valeurs de *Expr* dans le groupe.
Si l’entrée `summarize` à l’opérateur n’est pas triée, l’ordre des éléments dans le tableau résultant n’est pas défini.
Si l’entrée `summarize` à l’opérateur est triée, l’ordre des éléments dans le tableau résultant suit celui de l’entrée.

> [!TIP]
> Utilisez [`mv-apply`](./mv-applyoperator.md) l’opérateur pour créer une liste commandée par une clé. Consultez les exemples [ici](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).

**Voir aussi**

[`make_list_if`](./makelistif-aggfunction.md)l’opérateur `make_list`est similaire à , sauf qu’il accepte également un prédicat.