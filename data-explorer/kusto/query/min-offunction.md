---
title: min_of ()-Azure Explorateur de données
description: Cet article décrit min_of () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ed8d14a4e793f253342c1b52269678874c96660f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346760"
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
