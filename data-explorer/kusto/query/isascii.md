---
title: isascii ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit isascii () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d8a060e4a332988fd966e0dec9ed07b3c76d0e3f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347287"
---
# <a name="isascii"></a>isascii()

Retourne `true` si l’argument est une chaîne ASCII valide.
    
```kusto
isascii("some string") == true
```

## <a name="syntax"></a>Syntaxe

`isascii(`[*valeur*]`)`

## <a name="returns"></a>Retourne

Indique si l’argument est une chaîne ASCII valide.

## <a name="example"></a>Exemple

```kusto
T
| where isascii(fieldName)
| count
```