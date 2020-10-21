---
title: arg_min () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit arg_min () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 5c3e7912839dd3258c4a3f96530fbf438bf4ef4f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245600"
---
# <a name="arg_min-aggregation-function"></a>arg_min () (fonction d’agrégation)

Recherche une ligne dans le groupe qui réduit *ExprToMinimize*et retourne la valeur de *ExprToReturn* (ou `*` pour retourner la ligne entière).

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

`summarize`[ `(` *NameExprToMinimize* `,` *NameExprToReturn* [ `,` ...] `)=` ] `arg_min` `(` *ExprToMinimize*, `*`  |  *ExprToReturn* [ `,` ...]`)`

## <a name="arguments"></a>Arguments

* *ExprToMinimize*: expression qui sera utilisée pour le calcul de l’agrégation. 
* *ExprToReturn*: expression qui sera utilisée pour retourner la valeur lorsque *ExprToMinimize* est minimal. L’expression à retourner peut être un caractère générique (*) pour retourner toutes les colonnes de la table d’entrée.
* *NameExprToMinimize*: nom facultatif de la colonne de résultat représentant *ExprToMinimize*.
* *NameExprToReturn*: noms facultatifs supplémentaires pour les colonnes de résultats représentant *ExprToReturn*.

## <a name="returns"></a>Retours

Recherche une ligne dans le groupe qui réduit *ExprToMinimize*et retourne la valeur de *ExprToReturn* (ou `*` pour retourner la ligne entière).

## <a name="examples"></a>Exemples

Afficher le fournisseur le moins cher de chaque produit :

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

Affichez tous les détails, pas seulement le nom du fournisseur :

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

Recherchez la ville Southernmost dans chaque continent, avec son pays :

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/arg-min-aggfunction/arg-min.png" alt-text="Argument min.":::
