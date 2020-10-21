---
title: to_utf8 ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit to_utf8 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c47c37dbbde7bd2276f1b5788dc6eb7b062f39a3
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251268"
---
# <a name="to_utf8"></a>to_utf8()

Retourne un tableau dynamique des caractères Unicode d’une chaîne d’entrée (opération inverse de make_string).

## <a name="syntax"></a>Syntaxe

`to_utf8(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: chaîne source à convertir.

## <a name="returns"></a>Retours

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
