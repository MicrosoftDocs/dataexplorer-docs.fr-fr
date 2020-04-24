---
title: make_string() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_string() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d5af7cab9106088064048c1077ec3f9b1950ec08
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512629"
---
# <a name="make_string"></a>make_string()

Retourne la chaîne générée par les caractères Unicode.
    
**Syntaxe**

`make_string (`*Arg1* *[Argn]...*`)`

**Arguments**

* *Arg1* ... *ArgN* : expressions integers (int ou long) ou une valeur dynamique détenant un éventail de nombres intégrals.

* Cette fonction reçoit jusqu’à 64 arguments. 

**Retourne**

Retourne la chaîne faite des caractères Unicode dont la valeur de point de code est fournie par les arguments à cette fonction. L’entrée doit se composer de points de code Unicode valides.
Si un argument n’est pas cartographié sur un char Unicode, la fonction revient nulle.

**Exemples**

```kusto
print str = make_string(75, 117, 115, 116, 111)
```

|str|
|---|
|Kusto|
    
```kusto
print str = make_string(dynamic([75, 117, 115, 116, 111]))
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(dynamic([75, 117, 115]), 116, 111)
```

|str|
|---|
|Kusto|

```kusto
print str = make_string(75, 10, 117, 10, 115, 10, 116, 10, 111)
```

|str|
|---|
|K<br>u<br>s<br>t<br>o|