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
ms.openlocfilehash: 1d4dafdab4c727b89838f18e13b016d771262f08
ms.sourcegitcommit: a4779e31a52d058b07b472870ecd2b8b8ae16e95
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89366008"
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
> Utilisez l' [`mv-apply`](./mv-applyoperator.md) opérateur pour créer une liste ordonnée à l’aide d’une clé. Consultez les exemples [ici](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-make_list-aggregate-by-some-key).
