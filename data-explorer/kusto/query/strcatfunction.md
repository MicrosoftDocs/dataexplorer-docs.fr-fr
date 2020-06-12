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
ms.openlocfilehash: af25bb0407c9bc0c004c2f22e326ac034c6682cd
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717102"
---
# <a name="strcat"></a>strcat()

Concatène entre 1 et 64 arguments.

* Si les arguments ne sont pas de type chaîne, ils seront convertis en chaîne.

**Syntaxe**

`strcat(`*argument1*, *argument2*[, *argumentN*]`)`

**Arguments**

* *argument1* ... *argumentN*: expressions à concaténer.

**Retourne**

Les arguments, concaténés à une chaîne unique.

**Exemples**
  
   ```kusto
print str = strcat("hello", " ", "world")
```

|str|
|---|
|hello world|
