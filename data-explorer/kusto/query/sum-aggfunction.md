---
title: Sum () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Sum () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 3053a2c3bd423a2e1b8a2690bcdf54de9f1a1e36
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350823"
---
# <a name="sum-aggregation-function"></a>Sum () (fonction d’agrégation)

Calcule la somme de *expr* dans le groupe. 

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

résumer `sum(` *expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation. 

## <a name="returns"></a>Retourne

Valeur SUM de *expr* dans le groupe.
 