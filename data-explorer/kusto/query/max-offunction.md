---
title: max_of ()-Azure Explorateur de données
description: Cet article décrit max_of () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2045436de09bc31fa0378824310fa872478b861
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346828"
---
# <a name="max_of"></a>max_of()

Retourne la valeur maximale de plusieurs expressions numériques évaluées.

```kusto
max_of(10, 1, -3, 17) == 17
```

## <a name="syntax"></a>Syntaxe

`max_of``(` *expr_1* `,` *expr_2* ...`)`

## <a name="arguments"></a>Arguments

* *expr_i*: expression scalaire, à évaluer.

- Tous les arguments doivent être du même type.
- Le nombre maximal d’arguments 64 est pris en charge.

## <a name="returns"></a>Retourne

Valeur maximale de toutes les expressions d’arguments.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|
