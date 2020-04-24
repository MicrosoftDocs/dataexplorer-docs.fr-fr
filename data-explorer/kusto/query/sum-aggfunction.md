---
title: sum() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la somme () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b729d9be8aba9683a053ef80acb3936c0c64d6a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506679"
---
# <a name="sum-aggregation-function"></a>sum() (fonction d’agrégation)

Calcule la somme *d’Expr* dans l’ensemble du groupe. 

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

résumer `sum(` *Expr*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

La valeur somme *d’Expr* dans l’ensemble du groupe.
 