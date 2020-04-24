---
title: countif() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit countif() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 03b6f1cb959706463a73d8aa18a11144e2123492
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516981"
---
# <a name="countif-aggregation-function"></a>countif() (fonction d’agrégation)

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`.

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

Voir aussi - [compter()](count-aggfunction.md) fonction, qui compte les lignes sans expression prédicat.

**Syntaxe**

résumer `countif(` *Predicate*`)`

**Arguments**

* *Prédication*: Expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`.

> [!TIP]
> Utiliser `summarize countif(filter)` à la place de `where filter | summarize count()`