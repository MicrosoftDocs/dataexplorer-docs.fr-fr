---
title: make_list_with_nulls() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_list_with_nulls () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/09/2020
ms.openlocfilehash: 4b039008c5969cf02187d69a3486b09e04ec41ae
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512867"
---
# <a name="make_list_with_nulls-aggregation-function"></a>make_list_with_nulls)) (fonction d’agrégation)

Retourne `dynamic` un tableau (JSON) de toutes les valeurs d’Expr dans le groupe, y compris les valeurs nulles. *Expr*

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize``make_list_with_nulls(` *Expr Expr*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation.

**Retourne**

Retourne `dynamic` un tableau (JSON) de toutes les valeurs d’Expr dans le groupe, y compris les valeurs nulles. *Expr*
Si l’entrée `summarize` à l’opérateur n’est pas triée, l’ordre des éléments dans le tableau résultant n’est pas défini.
Si l’entrée `summarize` à l’opérateur est triée, l’ordre des éléments dans le tableau résultant suit celui de l’entrée.

> [!TIP]
> Utilisez [`mv-apply`](./mv-applyoperator.md) l’opérateur pour créer une liste commandée par une clé. Consultez les exemples [ici](./mv-applyoperator.md#using-mv-apply-operator-to-sort-the-output-of-makelist-aggregate-by-some-key).
