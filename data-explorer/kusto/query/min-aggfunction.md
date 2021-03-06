---
title: min () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit min () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: c6cd5b219b1f89e68d34b37b21135e5ec02d6ee8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248942"
---
# <a name="min-aggregation-function"></a>min () (fonction d’agrégation)

Retourne la valeur minimale dans le groupe. 

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

`summarize``min(` *Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

## <a name="returns"></a>Retours

Valeur minimale de *expr* dans le groupe.
 
> [!TIP]
> Cela vous donne la valeur minimale ou maximale, par exemple, le prix le plus élevé ou le plus bas. Toutefois, si vous souhaitez d’autres colonnes dans la ligne, par exemple, le nom du fournisseur ayant le prix le plus bas, utilisez [ARG_MAX](arg-max-aggfunction.md) ou [arg_min](arg-min-aggfunction.md).