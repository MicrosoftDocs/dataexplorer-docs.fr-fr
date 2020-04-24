---
title: max_of() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit max_of() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 68188ccd5eb814a22be166b8847d80193172813f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512493"
---
# <a name="max_of"></a>max_of()

Retourne la valeur maximale de plusieurs expressions numériques évaluées.

```kusto
max_of(10, 1, -3, 17) == 17
```

**Syntaxe**

`max_of`expr_1 *expr_2* ... *expr_1* `(``,``)`

**Arguments**

* *expr_i*: Une expression scalaire, à évaluer.

- Tous les arguments doivent être du même type.
- Un maximum de 64 arguments est étayé.

**Retourne**

La valeur maximale de toutes les expressions d’argument.

**Exemple**

```kusto
print result = max_of(10, 1, -3, 17) 
```

|result|
|---|
|17|