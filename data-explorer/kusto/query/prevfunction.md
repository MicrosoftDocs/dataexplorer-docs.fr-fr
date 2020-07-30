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
ms.openlocfilehash: fb781834d77aee678103a14714721ff0d46f7b3a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346097"
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
