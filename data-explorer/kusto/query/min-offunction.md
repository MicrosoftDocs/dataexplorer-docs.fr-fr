---
title: min_of() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit min_of() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 06a8f7ce6bcef8f3c15c4ea3d4c997b4e4540bf7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512391"
---
# <a name="min_of"></a>min_of()

Retourne la valeur minimale de plusieurs expressions numériques évaluées.

```kusto
min_of(10, 1, -3, 17) == -3
```

**Syntaxe**

`min_of`expr_1 *expr_2* ... *expr_1* `(``,``)`

**Arguments**

* *expr_i*: Une expression scalaire, à évaluer.

- Tous les arguments doivent être du même type.
- Un maximum de 64 arguments est étayé.

**Retourne**

La valeur minimale de toutes les expressions d’argument.

**Exemple**

```kusto
print result=min_of(10, 1, -3, 17) 
```

|result|
|---|
|-3|