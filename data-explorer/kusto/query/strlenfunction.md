---
title: strlen ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit strlen () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d28eae6852faedf2c6071164d8f80f9c3567602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350891"
---
# <a name="strlen"></a>strlen()

Retourne la longueur, en caractères, de la chaîne d’entrée.

## <a name="syntax"></a>Syntaxe

`strlen(`*code*`)`

## <a name="arguments"></a>Arguments

* *source*: chaîne source qui sera mesurée pour la longueur de chaîne.

## <a name="returns"></a>Retourne

Retourne la longueur, en caractères, de la chaîne d’entrée.

**Remarques**

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