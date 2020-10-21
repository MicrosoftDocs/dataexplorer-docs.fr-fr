---
title: Any () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit any () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4d718cfb46e3a404c943d579feaf4733499ab3e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248219"
---
# <a name="any-aggregation-function"></a>Any () (fonction d’agrégation)

Choisit arbitrairement un enregistrement pour chaque groupe dans un [opérateur de synthèse](summarizeoperator.md)et retourne la valeur d’une ou plusieurs expressions sur chaque enregistrement de ce type.

## <a name="syntax"></a>Syntaxe

`summarize``any` `(` (*Expr* [ `,` *expr2* ...]) | `*``)`

## <a name="arguments"></a>Arguments

* *Expr*: expression sur chaque enregistrement sélectionné à partir de l’entrée à retourner.
* *Expr2* .. *ExprN*: expressions supplémentaires.

## <a name="returns"></a>Retours

La `any` fonction d’agrégation retourne les valeurs des expressions calculées pour chacun des enregistrements, sélectionnées de façon aléatoire dans chaque groupe de l’opérateur de synthèse.

Si l' `*` argument est fourni, la fonction se comporte comme si les expressions étaient toutes des colonnes de l’entrée de l’opérateur de synthèse sous la forme des colonnes Group-by, le cas échéant.

**Remarques**

Cette fonction est utile lorsque vous souhaitez obtenir un exemple de valeur d’une ou de plusieurs colonnes par valeur de la clé de groupe composée.

Quand la fonction est fournie avec une référence de colonne unique, elle tente de retourner une valeur non NULL/non vide, si une telle valeur est présente.

En raison de la nature aléatoire de cette fonction, son utilisation multiple dans une application unique de l' `summarize` opérateur n’est pas équivalente à son utilisation une seule fois avec plusieurs expressions. La première peut avoir chaque application sélectionner un autre enregistrement, tandis que cette dernière garantit que toutes les valeurs sont calculées sur un enregistrement unique (par groupe distinct).

## <a name="examples"></a>Exemples

Afficher le continent aléatoire :

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="N’importe lequel 1":::

Afficher tous les détails d’un enregistrement aléatoire :

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggfunction/any2.png" alt-text="N’importe lequel 1":::

Affichez tous les détails pour chaque continent aléatoire :

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="N’importe lequel 1":::
