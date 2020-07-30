---
title: Max () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Max () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4d31f2137fcd64deab713522b1596f4c572606cc
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346845"
---
# <a name="max-aggregation-function"></a>Max () (fonction d’agrégation)

Retourne la valeur maximale dans le groupe. 

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

`summarize``max(` *Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

## <a name="returns"></a>Retourne

Valeur maximale de *expr* dans le groupe.
 
> [!TIP]
> Cela vous donne la valeur minimale ou maximale, par exemple, le prix le plus élevé ou le plus bas.
> Toutefois, si vous souhaitez d’autres colonnes dans la ligne, par exemple, le nom du fournisseur ayant le prix le plus bas, utilisez [ARG_MAX](arg-max-aggfunction.md) ou [arg_min](arg-min-aggfunction.md).