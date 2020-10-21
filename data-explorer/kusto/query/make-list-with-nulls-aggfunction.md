---
title: make_list_with_nulls () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit make_list_with_nulls () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 18d10aa15e25979c0945ec26efb8bfe9a66dfde4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252217"
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
