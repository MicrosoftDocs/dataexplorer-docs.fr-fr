---
title: strcat ()-Azure Explorateur de données
description: Cet article décrit strcat () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 55a52ff4aece3fa1a3197db0fb47984217292136
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251520"
---
# <a name="strcat"></a>strcat()

Concatène entre 1 et 64 arguments.

* Si les arguments ne sont pas de type chaîne, ils seront convertis en chaîne.

## <a name="syntax"></a>Syntaxe

`strcat(`*argument1*, *argument2*[, *argumentN*]`)`

## <a name="arguments"></a>Arguments

* *argument1* ... *argumentN*: expressions à concaténer.

## <a name="returns"></a>Retours

Les arguments, concaténés à une chaîne unique.

## <a name="examples"></a>Exemples
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
