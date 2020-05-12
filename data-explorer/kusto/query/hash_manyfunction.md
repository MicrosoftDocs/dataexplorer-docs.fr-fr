---
title: hash_many ()-Azure Explorateur de données
description: Cet article décrit hash_many () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/06/2020
ms.openlocfilehash: 66ca1e5ff330a4b39ab769b0e3e8d6359eed9c00
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226667"
---
# <a name="hash_many"></a>hash_many()

Retourne une valeur de hachage combinée de plusieurs valeurs.

**Syntaxe**

`hash_many(`*S1* `,` *S2* [ `,` *S3* ...]`)`

**Arguments**

* *S1*, *S2*,..., *sn*: valeurs d’entrée qui seront hachées ensemble.

**Retourne**

Valeur de hachage combinée des scalaires données.

**Exemples**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|valeur1|valeur2|associés|
|---|---|---|
|Hello|World (Monde)|-1440138333540407281|
