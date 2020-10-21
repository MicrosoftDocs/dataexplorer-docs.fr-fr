---
title: PREV ()-Azure Explorateur de données
description: Cet article décrit PREV () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0ef9fe5160d433554880ac1be0c4a3409d286f17
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249594"
---
# <a name="prev"></a>prev()

Retourne la valeur d’une colonne spécifique dans une ligne spécifiée.
La ligne spécifiée se trouve à un offset spécifié à partir de la ligne actuelle dans un [jeu de lignes sérialisé](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Syntaxe

Il existe plusieurs possibilités.

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

## <a name="arguments"></a>Arguments

* `column`: Colonne à partir de laquelle les valeurs sont extraites.

* `offset`: Décalage pour revenir en arrière dans les lignes. Quand aucun décalage n’est spécifié, un décalage par défaut de 1 est utilisé.

* `default_value`: Valeur par défaut à utiliser lorsqu’il n’existe aucune ligne précédente à partir de laquelle prendre la valeur. Si aucune valeur par défaut n’est spécifiée, la valeur null est utilisée.

## <a name="examples"></a>Exemples

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
