---
title: prev() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit prev() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33f6045333826b21ddc0092e2cc7d5e033c12a96
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744598"
---
# <a name="prev"></a>prev()

Retourne la valeur d’une colonne d’affilée qu’elle a à un certain décalage avant la ligne actuelle dans un [ensemble de rangée sérialisé](./windowsfunctions.md#serialized-row-set).

**Syntaxe**

`prev(column)`

`prev(column, offset)`

`prev(column, offset, default_value)`

**Arguments**

* `column`: la colonne pour obtenir les valeurs de.

* `offset`: le décalage pour revenir en rangs. Lorsqu’aucun décalage n’est spécifié, un décalage par défaut 1 est utilisé.

* `default_value`: la valeur par défaut à utiliser lorsqu’il n’y a pas de lignes précédentes pour prendre la valeur. Lorsqu’aucune valeur par défaut n’est spécifiée, null est utilisé.


**Exemples**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```