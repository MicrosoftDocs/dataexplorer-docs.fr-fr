---
title: make_set () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_set () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: e6eb481423e31e4dfa1b4e6c738ffb525e9aaef7
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618397"
---
# <a name="make_set-aggregation-function"></a>make_set () (fonction d’agrégation)

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes prises par *Expr* dans le groupe.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

**Syntaxe**

`summarize``make_set(` *Expr* [`,` *MaxSize*]`)`

**Arguments**

* *Expr*: expression pour le calcul de l’agrégation.
* *MaxSize* est une limite d’entier facultative sur le nombre maximal d’éléments retournés (la valeur par défaut est *1048576*). La valeur MaxSize ne peut pas dépasser 1048576.

> [!NOTE]
> Une variante héritée et obsolète de cette fonction `makeset()` : a une limite par défaut de *MaxSize* = 128.

**Retourne**

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes prises par *Expr* dans le groupe.
L’ordre de tri du tableau n’est pas défini.

> [!TIP]
> Pour compter uniquement des valeurs distinctes, utilisez [DCount ()](dcount-aggfunction.md)

**Exemple**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

:::image type="content" source="images/makeset-aggfunction/makeset.png" alt-text="Makeset":::

**Voir aussi**

* Utilisez [`mv-expand`](./mvexpandoperator.md) l’opérateur pour la fonction opposée.
* [`make_set_if`](./makesetif-aggfunction.md)est semblable à `make_set`, sauf qu’il accepte également un prédicat.