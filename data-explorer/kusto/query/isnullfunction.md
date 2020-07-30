---
title: IsNull ()-Azure Explorateur de données
description: Cet article décrit IsNull () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d1bea6260ca86e6ca47be843a6acc4fb43a037b3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347168"
---
# <a name="isnull"></a>isnull()

Évalue son argument unique et retourne une `bool` valeur indiquant si l’argument correspond à une valeur null.

```kusto
isnull(parse_json("")) == true
```

## <a name="syntax"></a>Syntaxe

`isnull(`*Expr*`)`

## <a name="returns"></a>Retourne

True ou false, selon que la valeur est ou non null.

**Remarques**

* `string`les valeurs ne peuvent pas être null. Utilisez [IsEmpty](./isemptyfunction.md) pour déterminer si une valeur de type `string` est vide ou non.

|x                |`isnull(x)`|
|-----------------|-----------|
|`""`             |`false`    |
|`"x"`            |`false`    |
|`parse_json("")`  |`true`     |
|`parse_json("[]")`|`false`    |
|`parse_json("{}")`|`false`    |

## <a name="example"></a>Exemple

```kusto
T | where isnull(PossiblyNull) | count
```