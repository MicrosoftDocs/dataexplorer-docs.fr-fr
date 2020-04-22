---
title: n’importe() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit n’importe quel () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2a0b2aed48c9c5aa9d5b99bdb6cab68375827d2c
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030199"
---
# <a name="any-aggregation-function"></a>n’importe() (fonction d’agrégation)

Choisit arbitrairement un enregistrement pour chaque groupe dans un [opérateur de résumé,](summarizeoperator.md)et retourne la valeur d’une ou plusieurs expressions sur chacun de ces dossiers.

**Syntaxe**

`summarize``any` *Expr* `,` *Expr2* ( Expr [ Expr2 ...]) `(` `*``)`

**Arguments**

* *Expr*: Une expression sur chaque enregistrement sélectionné à partir de l’entrée pour revenir.
* *Expr2* .. *ExprN*: Expressions supplémentaires.

**Retourne**

La `any` fonction d’agrégation renvoie les valeurs des expressions calculées pour chacun des enregistrements, sélectionnées au hasard par chaque groupe de l’opérateur de résumé.

Si `*` l’argument est fourni, la fonction se comporte comme si les expressions sont toutes des colonnes de l’entrée à l’opérateur de résumé sauf les colonnes de groupe, le cas échéant.

**Remarques**

Cette fonction est utile lorsque vous souhaitez obtenir un échantillon de valeur d’une ou plusieurs colonnes par valeur de la clé du groupe composé.

Lorsque la fonction est fournie avec une référence de colonne unique, elle tentera de retourner une valeur non nulle/non vide, si cette valeur est présente.

En raison de la nature aléatoire de cette fonction, l’utiliser plusieurs fois dans une seule application de l’opérateur n’est pas équivalent à l’utiliser `summarize` une seule fois avec plusieurs expressions. Le premier peut faire sélectionner chaque application d’un enregistrement différent, tandis que la seconde garantit que toutes les valeurs sont calculées sur un seul enregistrement (par groupe distinct).

**Exemples**

Afficher Le continent aléatoire :

```kusto
Continents | summarize any(Continent)
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="N’importe quel 1":::

Afficher tous les détails d’un enregistrement aléatoire :

```kusto
Continents | summarize any(*)
```

:::image type="content" source="images/aggfunction/any2.png" alt-text="Tous les 2":::

Afficher tous les détails pour chaque continent aléatoire :

```kusto
Continents | summarize any(*) by Continent
```

:::image type="content" source="images/aggfunction/any3.png" alt-text="Tous les 3":::
