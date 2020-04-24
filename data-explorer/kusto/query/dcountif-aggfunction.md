---
title: dcountif() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit dcountif() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1cc3c17474db835f381cac32d6107acb312c3d11
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516046"
---
# <a name="dcountif-aggregation-function"></a>dcountif() (fonction d’agrégation)

Retourne une estimation du nombre de valeurs distinctes d’Expr de `true`lignes pour lesquelles *Predicate* évalue à . *Expr* 

* Peut être utilisé uniquement dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md).

Renseignez-vous sur [l’exactitude de l’estimation](dcount-aggfunction.md#estimation-accuracy).

**Syntaxe**

résumer `dcountif(` *Expr*, *Predicate*, [`,` *Précision*]`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: Expression qui sera utilisée pour filtrer les lignes.
* Si *Accuracy* est spécifié, détermine le compromis entre vitesse et précision.
    * `0` : calcul le moins précis, mais le plus rapide. Erreur de 1,6 %
    * `1`- le défaut, qui équilibre la précision et le temps de calcul; d’environ 0,8 % d’erreur.
    * `2`- calcul précis et lent; d’environ 0,4 % d’erreur.
    * `3`- calcul très précis et lent; environ 0,28% d’erreur.
    * `4`- calcul super précis et le plus lent; d’environ 0,2 % d’erreur.
    
**Retourne**

Retourne une estimation du nombre de valeurs distinctes d’Expr de `true` lignes pour lesquelles *Predicate* évalue dans le groupe. *Expr* 

**Exemple**

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**Conseil : Erreur de compensation**

`dcountif()`peut entraîner une erreur ponctuelle dans les cas de bord où toutes les lignes `Predicate` passent, ou aucune des lignes passent, l’expression