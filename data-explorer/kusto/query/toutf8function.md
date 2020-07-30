---
title: to_utf8 ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit to_utf8 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 891a2bb079136d9a7c21c1992b79e3e0eab4c970
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350670"
---
# <a name="to_utf8"></a>to_utf8()

Retourne un tableau dynamique des caractères Unicode d’une chaîne d’entrée (opération inverse de make_string).

## <a name="syntax"></a>Syntaxe

`to_utf8(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: chaîne source à convertir.

## <a name="returns"></a>Retourne

Retourne un tableau dynamique des caractères Unicode qui composent la chaîne fournie à cette fonction.
Consultez [`make_string()`](makestringfunction.md) )

## <a name="examples"></a>Exemples

```kusto
print arr = to_utf8("⒦⒰⒮⒯⒪")
```

|arr|
|---|
|[9382, 9392, 9390, 9391, 9386]|

```kusto
print arr = to_utf8("קוסטו - Kusto")
```

|arr|
|---|
|[1511, 1493, 1505, 1496, 1493, 32, 45, 32, 75, 117, 115, 116, 111]|

```kusto
print str = make_string(to_utf8("Kusto"))
```

|str|
|---|
|Kusto|
