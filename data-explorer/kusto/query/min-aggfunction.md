---
title: min() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit min() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/24/2019
ms.openlocfilehash: ca50210c84b39f19e6717b27089313d0d116e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512408"
---
# <a name="min-aggregation-function"></a>min() (fonction d’agrégation)

Retourne la valeur minimale dans l’ensemble du groupe. 

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize``min(` *Expr Expr*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

La valeur minimale *d’Expr* dans l’ensemble du groupe.
 
> [!TIP]
> Cela vous donne le min ou le max sur son propre - par exemple, le prix le plus élevé ou le plus bas. Mais si vous voulez d’autres colonnes dans la rangée - par exemple, le nom du fournisseur avec le prix le plus bas - utiliser [arg_max](arg-max-aggfunction.md) ou [arg_min](arg-min-aggfunction.md).