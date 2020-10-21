---
title: IsEmpty ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IsEmpty () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2944e90f14f38e2f136f5815a95584edc50d8235
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251719"
---
# <a name="isempty"></a>isempty()

Retourne `true` si l’argument est une chaîne vide ou s’il a la valeur null.
    
```kusto
isempty("") == true
```

## <a name="syntax"></a>Syntaxe

`isempty(`[*valeur*]`)`

## <a name="returns"></a>Retours

Indique si l’argument est une chaîne vide ou s’il a la valeur isnull.

|x|isempty(x)
|---|---
| "" | true
|"x" | false
|parsejson("")|true
|parsejson("[]")|false
|parseJSON (" {} ")|false

## <a name="example"></a>Exemple

```kusto
T
| where isempty(fieldName)
| count
```