---
title: reverse() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’inverse () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e95246e0586dff7dd89dc2658c7fae08b1bbaddf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510300"
---
# <a name="reverse"></a>reverse()

Fonction rend l’inverse de la chaîne d’entrée.

Si la valeur d’entrée n’est pas de type de chaîne, la fonction jette de force la valeur à la chaîne.

**Syntaxe**

`reverse(`*Source*`)`

**Arguments**

* *source*: valeur d’entrée.  

**Retourne**

L’ordre inverse d’une valeur de chaîne.

**Exemples**

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|str|rstr (rstr)|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|ZYXWVUTSRQPONMLKJIHGFEDCBA|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble (rdouble)|rdatetime (en)|rtimespan (rtimespan)|
|---|---|---|---|
|54321|54.321|Z0000000.00:00:21T51-01-7102|00:00:30|




 