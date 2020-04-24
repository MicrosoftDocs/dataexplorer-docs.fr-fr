---
title: strrep() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit strrep() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 39b398e8fadb400c25cfeb97487c2ecf0669ad83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506713"
---
# <a name="strrep"></a>strrep()

Répétitions données [chaîne](./scalar-data-types/string.md) fourni quantité de fois.

* Dans le cas où le premier ou le troisième argument n’est pas d’un type de chaîne, il sera converti de force en chaîne.

**Syntaxe**

`strrep(`*valeur*,*multiplicateur*,[*delimiter*]`)`

**Arguments**

* *valeur*: expression d’entrée
* *multiplicateur*: valeur positive de l’intégrateur (de 1 à 1024)
* *delimiter*: une expression de chaîne facultative (par défaut : chaîne vide)

**Retourne**

Valeur répétée pour un nombre spécifié de fois, concatenated avec *delimiter*.

Dans le cas où le *multiplicateur* est plus que la valeur maximale autorisée (1024), la chaîne d’entrée sera répétée 1024 fois.
 
**Exemple**

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC (ABCABC)|123.123.123|00:00:03 00:00:03|