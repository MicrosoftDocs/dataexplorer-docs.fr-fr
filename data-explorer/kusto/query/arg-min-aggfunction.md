---
title: arg_min() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit arg_min() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/12/2019
ms.openlocfilehash: 4531bd498cf9d9053d57d8b463c7b0adfcc9bf8d
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663987"
---
# <a name="arg_min-aggregation-function"></a>arg_min)) (fonction d’agrégation)

Trouve une ligne dans le groupe qui minimise *ExprToMinimize*, et retourne `*` la valeur *d’ExprToReturn* (ou de retourner la rangée entière).

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize`[`(`*NameExprToMinimize* `,` *NameExprToReturn* [`,` ...] `)=` `arg_min` ] `(` *ExprToMinimize*, `*`  |  *ExprToReturn* [`,` ...]`)`

**Arguments**

* *ExprToMinimize*: Expression qui sera utilisée pour le calcul de l’agrégation. 
* *ExprToReturn*: Expression qui sera utilisée pour le retour de la valeur lorsque *ExprToMinimize* est minimum. L’expression au retour peut être une wildcard pour retourner toutes les colonnes de la table d’entrée.
* *NomExprToMinimize*: Un nom optionnel pour la colonne de résultat représentant *ExprToMinimize*.
* *NomExprToReturn*: Noms optionnels supplémentaires pour les colonnes de résultat représentant *ExprToReturn*.

**Retourne**

Trouve une ligne dans le groupe qui minimise *ExprToMinimize*, et retourne `*` la valeur *d’ExprToReturn* (ou de retourner la rangée entière).

**Exemples**

Afficher le fournisseur le moins cher de chaque produit :

```kusto
Supplies | summarize arg_min(Price, Supplier) by Product
```

Afficher tous les détails, pas seulement le nom du fournisseur:

```kusto
Supplies | summarize arg_min(Price, *) by Product
```

Trouvez la ville la plus méridionale de chaque continent, avec son pays :

```kusto
PageViewLog 
| summarize (latitude, min_lat_City, min_lat_country)=arg_min(latitude, City, country) 
    by continent
```

:::image type="content" source="images/aggregations/arg-min.png" alt-text="Arg min":::
