---
title: make_string ()-Azure Explorateur de données
description: Cet article décrit make_string () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 035be5910d173c75e585baa0b093dc6276bd4d63
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818583"
---
# <a name="make_string"></a>make_string()

Retourne la chaîne générée par les caractères Unicode.
    
**Syntaxe**

`make_string (`*Arg1*[, *ArgN*]...`)`

**Arguments**

* *Arg1* ... *ArgN*: expressions qui sont des entiers (int ou long) ou une valeur dynamique contenant un tableau de nombres entiers.

* Cette fonction reçoit jusqu’à 64 arguments.

**Retourne**

Retourne la chaîne composée des caractères Unicode dont la valeur de point de point est fournie par les arguments à cette fonction. L’entrée doit être composée de points de dépassements Unicode valides.
Si un argument n’est pas mappé à un caractère Unicode, la fonction retourne `null` .

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
