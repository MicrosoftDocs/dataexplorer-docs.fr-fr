---
title: isascii () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit isascii() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: daba0f4015a4847155309964f8ac0909ff4bc9d0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513717"
---
# <a name="isascii"></a>isascii()

Retourne `true` si l’argument est une chaîne ascii valide.
    
```kusto
isascii("some string") == true
```

**Syntaxe**

`isascii(`[*valeur*]`)`

**Retourne**

Indique si l’argument est une chaîne ascii valide.

**Exemple**

```kusto
T
| where isascii(fieldName)
| count
```