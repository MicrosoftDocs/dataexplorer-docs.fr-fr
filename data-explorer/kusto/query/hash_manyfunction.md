---
title: hash_many() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit hash_many() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: e98f9d1d956d15cd7a61e7873f9b1dd34c6ae288
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514176"
---
# <a name="hash_many"></a>hash_many()

Retourne une valeur combinée de hachage de valeurs multiples.

**Syntaxe**

`hash_many(`*s1* `,` *s2* [`,` *s3* ...]`)`

**Arguments**

* *s1*, *s2*, ..., *sN*: valeurs d’entrée qui seront hachées ensemble.

**Retourne**

La valeur combinée de hachage des scalars donnés.

**Exemples**

```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|valeur1|valeur2|Combiné|
|---|---|---|
|Hello|World (Monde)|-1440138333540407281|