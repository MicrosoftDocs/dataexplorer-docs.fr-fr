---
title: isutf8 ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit isutf8 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c9ad35964eb6d5a8c4a38b5ecdeb1eae43221d52
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247346"
---
# <a name="isutf8"></a>isutf8()

Retourne `true` si l’argument est une chaîne UTF8 valide.
    
```kusto
isutf8("some string") == true
```

## <a name="syntax"></a>Syntaxe

`isutf8(`[*valeur*]`)`

## <a name="returns"></a>Retours

Indique si l’argument est une chaîne UTF8 valide.

## <a name="example"></a>Exemple

```kusto
T
| where isutf8(fieldName)
| count
```