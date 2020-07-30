---
title: Any () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit any () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 73c3a660dc7a34f1f9fef840b13f47c13b4d1b2f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349735"
---
# <a name="any-aggregation-function"></a>Any () (fonction d’agrégation)

Choisit arbitrairement un enregistrement pour chaque groupe dans un [opérateur de synthèse](summarizeoperator.md)et retourne la valeur d’une ou plusieurs expressions sur chaque enregistrement de ce type.

## <a name="syntax"></a>Syntaxe

`summarize``any` `(` (*Expr* [ `,` *expr2* ...]) | `*``)`

## <a name="arguments"></a>Arguments

* *Expr*: expression sur chaque enregistrement sélectionné à partir de l’entrée à retourner.
* *Expr2* .. *ExprN*: expressions supplémentaires.

## <a name="returns"></a>Retourne

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

:::image type="content" source="images/aggfunction/any2.png" alt-text="N’importe laquelle 2":::

Affichez tous les détails pour chaque continent aléatoire :

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="N’importe laquelle 3":::
