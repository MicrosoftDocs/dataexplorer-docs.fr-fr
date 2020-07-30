---
title: strrep ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit strrep () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 797684aa70cb8205463cc41558afec82449c433d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350874"
---
# <a name="strrep"></a>strrep()

Répète la [chaîne](./scalar-data-types/string.md) fournie, le nombre de fois spécifié.

* Si le premier ou le troisième argument n’est pas de type chaîne, il est converti en chaîne.

## <a name="syntax"></a>Syntaxe

`strrep(`*valeur*,*multiplicateur*, [*délimiteur*]`)`

## <a name="arguments"></a>Arguments

* *valeur*: expression d’entrée
* *multiplicateur*: valeur entière positive (de 1 à 1024)
* *délimiteur*: expression de chaîne facultative (valeur par défaut : chaîne vide).

## <a name="returns"></a>Retourne

Valeur répétée pour un nombre de fois spécifié, concaténée avec le *délimiteur*.

Si le *multiplicateur* est supérieur à la valeur maximale autorisée (1024), la chaîne d’entrée est répétée 1024 fois.
 
## <a name="example"></a>Exemple

```kusto
print from_str = strrep('ABC', 2), from_int = strrep(123,3,'.'), from_time = strrep(3s,2,' ')
```

|from_str|from_int|from_time|
|---|---|---|
|ABCABC|123.123.123|00:00:03 00:00:03|