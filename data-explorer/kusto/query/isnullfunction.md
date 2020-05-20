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
ms.openlocfilehash: 4c57c7aba2bff2dfaecfa72b20ab76cc84ed17d6
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550586"
---
# <a name="isnull"></a>isnull()

Évalue son argument unique et retourne une `bool` valeur indiquant si l’argument correspond à une valeur null.

```kusto
isnull(parse_json("")) == true
```

**Syntaxe**

`isnull(`*Expr*`)`

**Retourne**

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

**Exemple**

```kusto
T | where isnull(PossiblyNull) | count
```