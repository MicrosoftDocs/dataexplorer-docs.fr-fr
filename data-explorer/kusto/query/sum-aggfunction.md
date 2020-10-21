---
title: Sum () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Sum () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: cb2bb49a0580819a172657ba4258fad08457d1d7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251368"
---
# <a name="sum-aggregation-function"></a>Sum () (fonction d’agrégation)

Calcule la somme de *expr* dans le groupe. 

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `sum(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

## <a name="returns"></a>Retours

Valeur SUM de *expr* dans le groupe.
 