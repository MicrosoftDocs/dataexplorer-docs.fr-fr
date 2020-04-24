---
title: to_utf8() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit to_utf8() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9d48ed99e517e0b1e5d498e80deaa48dc1cd3601
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505829"
---
# <a name="to_utf8"></a>to_utf8()

Retourne un tableau dynamique des caractères unicode d’une chaîne d’entrée (le fonctionnement inverse de make_string).

**Syntaxe**

`to_utf8(`*Source*`)`

**Arguments**

* *source*: La chaîne source à convertir.

**Retourne**

Retourne un tableau dynamique des caractères unicode qui composent la chaîne fournie à cette fonction.
Voir [`make_string()`](makestringfunction.md))

**Exemples**

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|Arr|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|Arr|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
