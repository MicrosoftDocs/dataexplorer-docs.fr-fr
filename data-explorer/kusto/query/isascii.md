---
title: isascii ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit isascii () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a17836597277b2cf5401f2caeaa60b44da731dd5
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251779"
---
# <a name="isascii"></a>isascii()

Retourne `true` si l’argument est une chaîne ASCII valide.
    
```kusto
isascii("some string") == true
```

## <a name="syntax"></a>Syntaxe

`isascii(`[*valeur*]`)`

## <a name="returns"></a>Retours

Indique si l’argument est une chaîne ASCII valide.

## <a name="example"></a>Exemple

```kusto
T
| where isascii(fieldName)
| count
```