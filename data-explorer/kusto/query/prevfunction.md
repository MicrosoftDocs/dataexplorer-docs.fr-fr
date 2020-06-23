---
title: PREV ()-Azure Explorateur de données
description: Cet article décrit PREV () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4216f691345c7dffd3bb1974e5f82e877ffb70f2
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128986"
---
# <a name="prev"></a>prev()

Retourne la valeur d’une colonne spécifique dans une ligne spécifiée.
La ligne spécifiée se trouve à un offset spécifié à partir de la ligne actuelle dans un [jeu de lignes sérialisé](./windowsfunctions.md#serialized-row-set).

**Syntaxe**

Il existe plusieurs possibilités.

* `prev(column)`

* `prev(column, offset)`

* `prev(column, offset, default_value)`

**Arguments**

* `column`: Colonne à partir de laquelle les valeurs sont extraites.

* `offset`: Décalage pour revenir en arrière dans les lignes. Quand aucun décalage n’est spécifié, un décalage par défaut de 1 est utilisé.

* `default_value`: Valeur par défaut à utiliser lorsqu’il n’existe aucune ligne précédente à partir de laquelle prendre la valeur. Si aucune valeur par défaut n’est spécifiée, la valeur null est utilisée.

**Exemples**

```kusto
Table | serialize | extend prevA = prev(A,1)
| extend diff = A - prevA
| where diff > 1

Table | serialize prevA = prev(A,1,10)
| extend diff = A - prevA
| where diff <= 10
```
