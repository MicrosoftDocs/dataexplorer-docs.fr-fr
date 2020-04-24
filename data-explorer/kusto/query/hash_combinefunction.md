---
title: hash_combine() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit hash_combine() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: d0c6375436df298a97c03ec2f06f7cd492d59d7f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514261"
---
# <a name="hash_combine"></a>hash_combine()

Combine les valeurs de hachage de deux hachages ou plus.

**Syntaxe**

`hash_combine(`*h1* `,` *h2* [`,` *h3* ...]`)`

**Arguments**

* *h1*: Valeur longue représentant la première valeur de hachage.
* *h2*: Valeur longue représentant la deuxième valeur de hachage.
* *hN*: Valeur longue représentant la valeur de hachage Nth.

**Retourne**

La valeur combinée de hachage des scalars donnés.

**Exemples**

```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|valeur1|valeur2|h1|h2|Combiné|
|---|---|---|---|---|
|Hello|World (Monde)|753694413698530628|1846988464401551951|-1440138333540407281|