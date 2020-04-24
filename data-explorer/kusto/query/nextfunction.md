---
title: next() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit ensuite () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f45e88942fdf9eb23451e1391866f57ca5f0e21a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512119"
---
# <a name="next"></a>next()

Retourne la valeur d’une colonne d’affilée qu’elle à un certain décalage suivant la ligne actuelle dans un [ensemble de rangée sérialisé](./windowsfunctions.md#serialized-row-set).

**Syntaxe**

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

**Arguments**

* `column`: la colonne pour obtenir les valeurs de.

* `offset`: le décalage pour aller de l’avant en rangs. Lorsqu’aucun décalage n’est spécifié, un décalage par défaut 1 est utilisé.

* `default_value`: la valeur par défaut à utiliser lorsqu’il n’y a pas de prochaines lignes pour prendre la valeur. Lorsqu’aucune valeur par défaut n’est spécifiée, null est utilisé.


**Exemples**
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```