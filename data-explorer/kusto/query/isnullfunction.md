---
title: isull() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit isull() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e26dca661ceac1ad209358b24b3f8d497a5c3049
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513411"
---
# <a name="isnull"></a>isnull()

Évalue son seul argument `bool` et renvoie une valeur indiquant si l’argument évalue à une valeur nulle.

```kusto
isnull(parse_json("")) == true
```

**Syntaxe**

`isnull(`*Expr*`)`

**Retourne**

True ou false selon si la valeur est null ou not null.

**Remarques**

* `string`valeurs ne peuvent pas être nulles. [L’utilisation estempty](./isemptyfunction.md) pour déterminer `string` si une valeur de type est vide ou non.

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