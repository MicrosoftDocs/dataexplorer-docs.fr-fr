---
title: strcat_array() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit strcat_array () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2d2412762cf68243e3952a8ad12a5b919d947bd3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506951"
---
# <a name="strcat_array"></a>strcat_array()

Crée une chaîne concatenated de valeurs de tableau à l’aide de délimitation spécifiée.
    
**Syntaxe**

`strcat_array(`*tableau*, *delimiter*`)`

**Arguments**

* *tableau*: `dynamic` Une valeur représentant un éventail de valeurs à concatenated.
* *délimitant*: `string` Une valeur qui sera utilisée pour concatener les valeurs dans *le tableau*

**Retourne**

Valeurs de tableau, concatenated à une seule corde.

**Exemples**
  
```kusto
print str = strcat_array(dynamic([1, 2, 3]), "->")
```

|str|
|---|
|1->2->3|