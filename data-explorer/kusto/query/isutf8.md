---
title: isutf8 ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit isutf8 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 952ea030d351a9e23fe26bbd7f27a96d182a89e3
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347151"
---
# <a name="isutf8"></a>isutf8()

Retourne `true` si l’argument est une chaîne UTF8 valide.
    
```kusto
isutf8("some string") == true
```

## <a name="syntax"></a>Syntaxe

`isutf8(`[*valeur*]`)`

## <a name="returns"></a>Retourne

Indique si l’argument est une chaîne UTF8 valide.

## <a name="example"></a>Exemple

```kusto
T
| where isutf8(fieldName)
| count
```