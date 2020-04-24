---
title: isutf8 () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit isutf8() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 619fb90b72fed8ec0e10fe05ddc3c6df6ff1386e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513394"
---
# <a name="isutf8"></a>isutf8()

Retourne `true` si l’argument est une chaîne utf8 valide.
    
```kusto
isutf8("some string") == true
```

**Syntaxe**

`isutf8(`[*valeur*]`)`

**Retourne**

Indique si l’argument est une chaîne utf8 valide.

**Exemple**

```kusto
T
| where isutf8(fieldName)
| count
```