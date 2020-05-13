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
ms.openlocfilehash: 4b912b1bdc68d7b3071ace8547f0aaf7c679a86a
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271602"
---
# <a name="max_of"></a>max_of()

Retourne la valeur maximale de plusieurs expressions numériques évaluées.

```kusto
max_of(10, 1, -3, 17) == 17
```

**Syntaxe**

`max_of``(` *expr_1* `,` *expr_2* ...`)`

**Arguments**

* *expr_i*: expression scalaire, à évaluer.

- Tous les arguments doivent être du même type.
- Le nombre maximal d’arguments 64 est pris en charge.

**Retourne**

Valeur maximale de toutes les expressions d’arguments.

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|
