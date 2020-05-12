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
ms.openlocfilehash: 41f07f16641fd303c9b8e76b4924238378b6ccc9
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224814"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls () (fonction d’agrégation)

Retourne un `dynamic` tableau (JSON) de toutes les valeurs de *expr* dans le groupe, y compris les valeurs NULL.

* Peut être utilisé uniquement dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

**Syntaxe**

`summarize``make_list_with_nulls(` *Expr*`)`

**Arguments**

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation.

**Retourne**

Retourne un `dynamic` tableau (JSON) de toutes les valeurs de *expr* dans le groupe, y compris les valeurs NULL.
Si l’entrée de l' `summarize` opérateur n’est pas triée, l’ordre des éléments dans le tableau résultant n’est pas défini.
Si l’entrée de l' `summarize` opérateur est triée, l’ordre des éléments dans le tableau résultant suit celui de l’entrée.

> [!TIP]
> Utilisez l' [`mv-apply`](./mv-applyoperator.md) opérateur pour créer une liste ordonnée à l’aide d’une clé. Consultez les exemples [ici](./mv-applyoperator.md#using-the-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).
