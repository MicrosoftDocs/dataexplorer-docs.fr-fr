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
ms.openlocfilehash: f8abb1683a2f15f012e9a9271681688c19901af0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227591"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

Retourne le nombre de bits définis dans la représentation binaire d’un nombre.

```kusto
bitset_count_ones(42)
```

**Syntaxe**

`bitset_count_ones(`*num1*' ') '

**Arguments**

* *num1*: nombre long ou entier.

**Retourne**

Retourne le nombre de bits définis dans la représentation binaire d’un nombre.

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|ceux|
|---|
|3|
