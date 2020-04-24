---
title: isempty() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit isempty () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2cb0e53aa16257398c20661c31494ca9dda17c1e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513615"
---
# <a name="isempty"></a>isempty()

Retourne `true` si l’argument est une chaîne vide ou est nul.
    
```kusto
isempty("") == true
```

**Syntaxe**

`isempty(`[*valeur*]`)`

**Retourne**

Indique si l’argument est une chaîne vide ou s’il a la valeur isnull.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parsejson ("{}")|false

**Exemple**

```kusto
T
| where isempty(fieldName)
| count
```