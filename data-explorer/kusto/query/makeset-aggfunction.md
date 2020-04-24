---
title: make_set() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_set() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/23/2020
ms.openlocfilehash: db11bd528703323d54ff96b228c0b80bbcb86dd9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512680"
---
# <a name="make_set-aggregation-function"></a>make_set)) (fonction d’agrégation)

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes prises par *Expr* dans le groupe.

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize``make_set(` *Expr* `,` [ *MaxSize*]`)`

**Arguments**

* *Expr*: Expression pour calcul d’agrégation.
* *MaxSize* est une limite d’intégrisation facultative sur le nombre maximum d’éléments retournés (par défaut est *1048576*). La valeur MaxSize ne peut excéder 1048576.

> [!NOTE]
> Une variante héritée et `makeset()` obsolète de cette fonction : a une limite par défaut de *MaxSize* 128.

**Retourne**

Retourne un tableau (JSON) `dynamic` du jeu de valeurs distinctes prises par *Expr* dans le groupe.
L’ordre de tri du tableau n’est pas défini.

> [!TIP]
> Pour ne compter que les valeurs distinctes, utilisez [dcount()](dcount-aggfunction.md)

**Exemple**

```kusto
PageViewLog 
| summarize countries=make_set(country) by continent
```

![texte de remplacement](./images/aggregations/makeset.png "makeset")

**Voir aussi**

* Utilisez [`mv-expand`](./mvexpandoperator.md) l’opérateur pour la fonction opposée.
* [`make_set_if`](./makesetif-aggfunction.md)l’opérateur `make_set`est similaire à , sauf qu’il accepte également un prédicat.