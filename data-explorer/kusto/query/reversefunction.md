---
title: Reverse ()-Azure Explorateur de données
description: Cet article décrit l’inverse () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: fa87dae85b158e162cea90c276057abb210ea23b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345774"
---
# <a name="reverse"></a>reverse()

La fonction inverse l’ordre de la chaîne d’entrée.
Si la valeur d’entrée n’est pas de type `string` , la fonction effectue un cast forcé de la valeur en type `string` .

## <a name="syntax"></a>Syntaxe

`reverse(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: valeur d’entrée.  

## <a name="returns"></a>Retourne

Ordre inverse d’une valeur de chaîne.

## <a name="examples"></a>Exemples

```kusto
print str = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
| extend rstr = reverse(str)
```

|str|RSTR|
|---|---|
|ABCDEFGHIJKLMNOPQRSTUVWXYZ|ZYXWVUTSRQPONMLKJIHGFEDCBA|


```kusto
print ['int'] = 12345, ['double'] = 123.45, 
['datetime'] = datetime(2017-10-15 12:00), ['timespan'] = 3h
| project rint = reverse(['int']), rdouble = reverse(['double']), 
rdatetime = reverse(['datetime']), rtimespan = reverse(['timespan'])
```

|rint|rdouble|rdatetime|rtimespan|
|---|---|---|---|
|54321|54,321|Z 0000000.00:00:21T51-01-7102|00:00:30|
