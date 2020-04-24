---
title: Le type de données bool - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le type de données bool dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: dd9cfa4f97f3baa4353f967f5e1ca31186f4815f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509926"
---
# <a name="the-bool-data-type"></a>Le type de données bool

Le `bool` `boolean`type de données () peut `true` `false` avoir l’un `1` des `0`deux états: ou (encodé en interne comme et , respectivement), ainsi que la valeur nulle.

## <a name="bool-literals"></a>littérals bool

Le `bool` type de données a les littéral suivants:
* `true`et `bool(true)`: Représenter la vérêtise
* `false`et `bool(false)`: Représenter le mensonge
* `bool(null)`: Voir [les valeurs nulles](null-values.md)

## <a name="bool-operators"></a>opérateurs bool

Le `bool` type de données prend`==`en charge`!=`les opérateurs`and`suivants : égalité`or`( ), inégalité (), logique et ( ), logique-ou ().