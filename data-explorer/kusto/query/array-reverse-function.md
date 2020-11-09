---
title: array_reverse ()-Azure Explorateur de données
description: Cet article décrit array_reverse () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: 327564a953c8f537a0f76727b4313749f61eec3e
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94374004"
---
# <a name="array_reverse"></a>array_reverse ()

Inverse l’ordre des éléments dans un tableau dynamique.

## <a name="syntax"></a>Syntax

`array_reverse(`*array*`)`

## <a name="arguments"></a>Arguments

*tableau* : tableau d’entrée à inverser.

## <a name="returns"></a>Retours

Tableau qui contient exactement les mêmes éléments que le tableau d’entrée, mais dans l’ordre inverse.

## <a name="example"></a> Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print arr=dynamic(["this", "is", "an", "example"]) 
| project Result=array_reverse(arr)
```

|Résultats|
|---|
|["example", "a", "is", "This"]|
