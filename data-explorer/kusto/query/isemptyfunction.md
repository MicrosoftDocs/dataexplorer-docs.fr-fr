---
title: IsEmpty ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit IsEmpty () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ac2bf5d5ea55172cbdb07bf90704ae5ad497e925
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347270"
---
# <a name="isempty"></a>isempty()

Retourne `true` si l’argument est une chaîne vide ou s’il a la valeur null.
    
```kusto
isempty("") == true
```

## <a name="syntax"></a>Syntaxe

`isempty(`[*valeur*]`)`

## <a name="returns"></a>Retourne

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