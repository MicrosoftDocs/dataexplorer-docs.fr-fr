---
title: strcat_delim ()-Azure Explorateur de données
description: Cet article décrit strcat_delim () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f6a78a5abb92aa93fe8b1ae15ea8968f71bde07c
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264553"
---
# <a name="strcat_delim"></a>strcat_delim()

Concatène entre 2 et 64 arguments, avec le délimiteur, fourni comme premier argument.

 * Si les arguments ne sont pas de type chaîne, ils seront convertis en chaîne.

**Syntaxe**

`strcat_delim(`*Delimiter*, *argument1*, *argument2*[, *argumentN*]`)`

**Arguments**

* *délimiteur*: expression de chaîne qui sera utilisée comme séparateur.
* *argument1* ... *argumentN*: expressions à concaténer.

**Retourne**

Arguments, concaténés à une chaîne unique avec *délimiteur*.

**Exemples**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
