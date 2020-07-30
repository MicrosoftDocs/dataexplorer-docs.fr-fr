---
title: strcat ()-Azure Explorateur de données
description: Cet article décrit strcat () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f26b4bf267a4387748fe4c4c26636579607de51
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350993"
---
# <a name="strcat"></a>strcat()

Concatène entre 1 et 64 arguments.

* Si les arguments ne sont pas de type chaîne, ils seront convertis en chaîne.

## <a name="syntax"></a>Syntaxe

`strcat(`*argument1*, *argument2*[, *argumentN*]`)`

## <a name="arguments"></a>Arguments

* *argument1* ... *argumentN*: expressions à concaténer.

## <a name="returns"></a>Retourne

Les arguments, concaténés à une chaîne unique.

## <a name="examples"></a>Exemples
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
