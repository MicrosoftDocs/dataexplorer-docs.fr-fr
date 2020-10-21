---
title: dcountif () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit dcountif () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c14c4d9e0512d4bb054bb3df39acb4828be5a5bd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247543"
---
# <a name="dcountif-aggregation-function"></a>dcountif () (fonction d’agrégation)

Retourne une estimation du nombre de valeurs distinctes de *expr* de lignes pour lesquelles le *prédicat* a la valeur `true` . 

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md).

En savoir plus sur la précision de l' [estimation](dcount-aggfunction.md#estimation-accuracy).

## <a name="syntax"></a>Syntaxe

résumer `dcountif(` *expr*, *predicate*, [ `,` *précision*]`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation.
* *Predicate*: expression qui sera utilisée pour filtrer les lignes.
* Si *Accuracy* est spécifié, détermine le compromis entre vitesse et précision.
    * `0` : calcul le moins précis, mais le plus rapide. 1,6% d’erreurs
    * `1` = la valeur par défaut, qui équilibre l’exactitude et l’heure de calcul ; environ 0,8% d’erreurs.
    * `2` = calcul précis et lent ; environ 0,4% d’erreurs.
    * `3` = calcul plus précis et plus lent ; environ 0,28% d’erreurs.
    * `4` = le calcul plus précis et le plus lent ; environ 0,2% d’erreurs.
    
## <a name="returns"></a>Retours

Retourne une estimation du nombre de valeurs distinctes de *expr*  de lignes pour lesquelles le *prédicat* a `true` la valeur dans le groupe. 

## <a name="example"></a>Exemple

```kusto
PageViewLog | summarize countries=dcountif(country, country startswith "United") by continent
```

**Astuce : erreur de décalage**

`dcountif()` peut entraîner une erreur unique dans les cas de périphérie où toutes les lignes réussissent ou qu’aucune ligne ne passe, l' `Predicate` expression