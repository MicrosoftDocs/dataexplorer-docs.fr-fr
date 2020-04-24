---
title: bitset_count_ones() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit bitset_count_ones() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: c23e95ee9f00a0ca173d68d3591ad604b31dfac2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517389"
---
# <a name="bitset_count_ones"></a>bitset_count_ones()

Retourne le nombre de bits de set dans la représentation binaire d’un nombre.

```kusto
bitset_count_ones(42)
```

**Syntaxe**

`bitset_count_ones(`*num1*')'

**Arguments**

* *num1*: numéro long ou integer.

**Retourne**

Retourne le nombre de bits de set dans la représentation binaire d’un nombre.

**Exemple**

```kusto
// 42 = 32+8+2 : b'00101010' == 3 bits set
print ones = bitset_count_ones(42) 
```

|Ceux|
|---|
|3|