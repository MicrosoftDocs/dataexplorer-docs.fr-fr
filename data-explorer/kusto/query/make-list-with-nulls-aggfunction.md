---
title: make_list_with_nulls () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_list_with_nulls () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: c53faca94e273bf816abcfa34ed400708a7433a3
ms.sourcegitcommit: 62476f682b7812cd9cff7e6958ace5636ee46755
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92169555"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls () (fonction d’agrégation)

Retourne un `dynamic` tableau (JSON) de toutes les valeurs de *expr* dans le groupe, y compris les valeurs NULL.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

## <a name="syntax"></a>Syntaxe

`summarize``make_list_with_nulls(` *Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation.

## <a name="returns"></a>Retours

Retourne un `dynamic` tableau (JSON) de toutes les valeurs de *expr* dans le groupe, y compris les valeurs NULL.
Si l’entrée de l' `summarize` opérateur n’est pas triée, l’ordre des éléments dans le tableau résultant n’est pas défini.
Si l’entrée de l' `summarize` opérateur est triée, l’ordre des éléments dans le tableau résultant suit celui de l’entrée.

> [!TIP]
> Utilisez la [`array_sort_asc()`](./arraysortascfunction.md) [`array_sort_desc()`](./arraysortdescfunction.md) fonction ou pour créer une liste ordonnée à l’aide d’une clé.
