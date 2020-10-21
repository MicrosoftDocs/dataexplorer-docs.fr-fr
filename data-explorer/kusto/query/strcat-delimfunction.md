---
title: strcat_delim ()-Azure Explorateur de données
description: Cet article décrit strcat_delim () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: de34f4a2f6efb3af84b61f53da8d58cb60dea7ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243696"
---
# <a name="strcat_delim"></a>strcat_delim()

Concatène entre 2 et 64 arguments, avec le délimiteur, fourni comme premier argument.

 * Si les arguments ne sont pas de type chaîne, ils seront convertis en chaîne.

## <a name="syntax"></a>Syntaxe

`strcat_delim(`*Delimiter*, *argument1*, *argument2*[, *argumentN*]`)`

## <a name="arguments"></a>Arguments

* *délimiteur*: expression de chaîne qui sera utilisée comme séparateur.
* *argument1* ... *argumentN*: expressions à concaténer.

## <a name="returns"></a>Retours

Arguments, concaténés à une chaîne unique avec *délimiteur*.

## <a name="examples"></a>Exemples

```kusto
print st = strcat_delim('-', 1, '2', 'A', 1s)

```

|st|
|---|
|1-2-A-00:00:01|
