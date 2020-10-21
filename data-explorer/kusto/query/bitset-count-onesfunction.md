---
title: bitset_count_ones ()-Azure Explorateur de données
description: Cet article décrit bitset_count_ones () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: fb14ffa5807a61ade913631c7d6da6b23edc26d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245330"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

Retourne le nombre de bits définis dans la représentation binaire d’un nombre.

```kusto
bitset_count_ones(42)
```

## <a name="syntax"></a>Syntaxe

`bitset_count_ones(`*num1*' ') '

## <a name="arguments"></a>Arguments

* *num1*: nombre long ou entier.

## <a name="returns"></a>Retours

Retourne le nombre de bits définis dans la représentation binaire d’un nombre.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|ceux|
|---|
|3|
