---
title: strlen() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit strlen() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 33bb1ea56ebc03dc7357264bcdf987c191478cbb
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506866"
---
# <a name="strlen"></a>strlen()

Retourne la longueur, en caractères, de la chaîne d’entrée.

**Syntaxe**

`strlen(`*Source*`)`

**Arguments**

* *source*: La chaîne source qui sera mesurée pour la longueur des cordes.

**Retourne**

Retourne la longueur, en caractères, de la chaîne d’entrée.

**Remarques**

Chaque personnage Unicode dans la `1`chaîne est égal à , y compris les substituts.
(par exemple : les caractères chinois seront comptés une fois malgré le fait qu’il nécessite plus d’une valeur dans l’encodage UTF-8).


**Exemples**

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