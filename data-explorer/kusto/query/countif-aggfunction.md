---
title: NB.si () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit NB.si () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: a5b69b50c0cf4c07934d7900937675bf6338eab9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348766"
---
# <a name="countif-aggregation-function"></a>NB.si () (fonction d’agrégation)

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

Voir également la fonction [Count ()](count-aggfunction.md) , qui compte les lignes sans expression de prédicat.

## <a name="syntax"></a>Syntaxe

synthétiser le `countif(` *prédicat*`)`

## <a name="arguments"></a>Arguments

* *Predicate*: expression qui sera utilisée pour le calcul de l’agrégation. 

## <a name="returns"></a>Retourne

Retourne le nombre de lignes pour lesquelles *Predicate* a la valeur `true`.

> [!TIP]
> Utiliser `summarize countif(filter)` à la place de `where filter | summarize count()`