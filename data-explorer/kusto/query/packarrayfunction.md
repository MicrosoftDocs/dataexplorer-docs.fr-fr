---
title: pack_array() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit pack_array() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: ad13efd6b0743a3c092b2859ea032c3731ffdf1b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511864"
---
# <a name="pack_array"></a>pack_array()

Emballe toutes les valeurs d’entrée dans un tableau dynamique.

**Syntaxe**

`pack_array(`*Expr1*`[`` *Expr2*]`, )'

**Arguments**

* *Expr1... N*: Expressions d’entrée à emballer dans un tableau dynamique.

**Retourne**

Dynamic array qui comprend les valeurs d’Expr1, Expr2, ... , ExprN.

**Exemple**

```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| project pack_array(x,y,z)
```

|Colonne1|
|---|
|[1,2,4]|
|[2,4,8]|
|[3,6,12]|

```kusto
range x from 1 to 3 step 1
| extend y = tostring(x * 2)
| extend z = (x * 2) * 1s
| project pack_array(x,y,z)
```

|Colonne1|
|---|
|[1,"2","00:00:02"]|
|[2,"4","00:00:04"]|
|[3,"6","00:00:06"]|