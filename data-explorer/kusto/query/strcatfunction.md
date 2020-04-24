---
title: strcat() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit strcat() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: dd01f875b45be038371cc184987aa2a8f8b8d5eb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506917"
---
# <a name="strcat"></a>strcat()

Concatenates entre 1 et 64 arguments.

* Dans le cas où les arguments ne sont pas de type de chaîne, ils seront convertis de force en chaîne.

**Syntaxe**

`strcat(`*argument1*,*argument2* [, *argumentN*]`)`

**Arguments**

* *argument1* ... *argumentN* : expressions à concatenated.

**Retourne**

Arguments, concatenated à une seule corde.

**Exemples**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|