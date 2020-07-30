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
ms.openlocfilehash: 36d31e88a89f23006dac73b92777b13db4933d06
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346879"
---
# <a name="make_string"></a>make_string()

Retourne la chaîne générée par les caractères Unicode.
    
## <a name="syntax"></a>Syntaxe

`make_string (`*Arg1*[, *ArgN*]...`)`

## <a name="arguments"></a>Arguments

* *Arg1* ... *ArgN*: expressions qui sont des entiers (int ou long) ou une valeur dynamique contenant un tableau de nombres entiers.

* Cette fonction reçoit jusqu’à 64 arguments.

## <a name="returns"></a>Retours

Retourne la chaîne composée des caractères Unicode dont la valeur de point de point est fournie par les arguments à cette fonction. L’entrée doit être composée de points de dépassements Unicode valides.
Si un argument n’est pas mappé à un caractère Unicode, la fonction retourne `null` .

## <a name="examples"></a>Exemples

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
