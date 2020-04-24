---
title: max() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit max () (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: bbfc9591fb20903d18486f9f249d3b1240f705e3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512561"
---
# <a name="max-aggregation-function"></a>max() (fonction d’agrégation)

Retourne la valeur maximale dans l’ensemble du groupe. 

* Ne peut être utilisé que dans le contexte de l’agrégation à l’intérieur [résumer](summarizeoperator.md)

**Syntaxe**

`summarize``max(` *Expr Expr*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation. 

**Retourne**

La valeur maximale *d’Expr* dans l’ensemble du groupe.
 
> [!TIP]
> Cela vous donne le min ou le max sur son propre - par exemple, le prix le plus élevé ou le plus bas.
> Mais si vous voulez d’autres colonnes dans la rangée - par exemple, le nom du fournisseur avec le prix le plus bas - utiliser [arg_max](arg-max-aggfunction.md) ou [arg_min](arg-min-aggfunction.md).