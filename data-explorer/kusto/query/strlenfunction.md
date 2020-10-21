---
title: strlen ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit strlen () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4256218669685ec12a939156021803105e5ddfc6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248404"
---
# <a name="strlen"></a>strlen()

Retourne la longueur, en caractères, de la chaîne d’entrée.

## <a name="syntax"></a>Syntaxe

`strlen(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: chaîne source qui sera mesurée pour la longueur de chaîne.

## <a name="returns"></a>Retours

Retourne la longueur, en caractères, de la chaîne d’entrée.

**Notes**

Chaque caractère Unicode de la chaîne est égal à `1` , y compris les substituts.
(par exemple, les caractères chinois seront comptés une fois en dépit du fait qu’il requiert plus d’une valeur dans l’encodage UTF-8).


## <a name="examples"></a>Exemples

```kusto
print length = strlen("hello")
```

|length|
|---|
|5|

```kusto
print length = strlen("⒦⒰⒮⒯⒪")
```

|length|
|---|
|5|