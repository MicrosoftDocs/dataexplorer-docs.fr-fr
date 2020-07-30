---
title: bitset_count_ones ()-Azure Explorateur de données
description: Cet article décrit bitset_count_ones () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: b8ebef923d1cc67c118317680e1ec414900a469e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348953"
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

## <a name="returns"></a>Retourne

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
