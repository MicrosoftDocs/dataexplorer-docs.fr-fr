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
ms.openlocfilehash: 0762b1416df32279b9801c47f129a6966772a7e2
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271364"
---
# <a name="min_of"></a>min_of()

Retourne la valeur minimale de plusieurs expressions numériques évaluées.

```kusto
min_of(10, 1, -3, 17) == -3
```

**Syntaxe**

`min_of``(` *expr_1* `,` *expr_2* ...`)`

**Arguments**

* *expr_i*: expression scalaire, à évaluer.

- Tous les arguments doivent être du même type.
- Le nombre maximal d’arguments 64 est pris en charge.

**Retourne**

Valeur minimale de toutes les expressions d’arguments.

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|
