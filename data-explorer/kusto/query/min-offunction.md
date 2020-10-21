---
title: min_of ()-Azure Explorateur de données
description: Cet article décrit min_of () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c08ef6f147640330cd5ea33c55dcc6acafd77a31
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248826"
---
# <a name="min_of"></a>min_of()

Retourne la valeur minimale de plusieurs expressions numériques évaluées.

```kusto
min_of(10, 1, -3, 17) == -3
```

## <a name="syntax"></a>Syntaxe

`min_of``(` *expr_1* `,` *expr_2* ...`)`

## <a name="arguments"></a>Arguments

* *expr_i*: expression scalaire, à évaluer.

- Tous les arguments doivent être du même type.
- Le nombre maximal d’arguments 64 est pris en charge.

## <a name="returns"></a>Retours

Valeur minimale de toutes les expressions d’arguments.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|
