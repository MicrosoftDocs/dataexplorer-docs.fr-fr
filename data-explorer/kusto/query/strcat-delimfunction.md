---
title: strcat_delim() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit strcat_delim() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f944af741cd5f655c2c9b090ddebc6cc35a47766
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506934"
---
# <a name="strcat_delim"></a>strcat_delim()

Concatenates entre 2 et 64 arguments, avec délimitation, fourni comme premier argument.

 * Dans le cas où les arguments ne sont pas de type de chaîne, ils seront convertis de force en chaîne.

**Syntaxe**

`strcat_delim(`*dlimiter*,*argument1*,*argument2* [, *argumentN*]`)`

**Arguments**

* *délimitateur*: expression de cordes, qui sera utilisée comme séparateur.
* *argument1* ... *argumentN* : expressions à concatenated.

**Retourne**

Arguments, concatenated à une seule corde avec *delimiter*.

**Exemples**

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|