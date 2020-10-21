---
title: IsNull ()-Azure Explorateur de données
description: Cet article décrit IsNull () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: afaff2c00ca9136e113639deed886d039d21fda9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241530"
---
# <a name="isnull"></a>isnull()

Évalue son argument unique et retourne une `bool` valeur indiquant si l’argument correspond à une valeur null.

```kusto
isnull(parse_json("")) == true
```

## <a name="syntax"></a>Syntaxe

`isnull(`*Expr*`)`

## <a name="returns"></a>Retours

True ou false, selon que la valeur est ou non null.

**Notes**

* `string` les valeurs ne peuvent pas être null. Utilisez [IsEmpty](./isemptyfunction.md) pour déterminer si une valeur de type `string` est vide ou non.

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