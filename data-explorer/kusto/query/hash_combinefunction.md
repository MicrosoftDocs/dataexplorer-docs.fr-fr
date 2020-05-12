---
title: hash_combine ()-Azure Explorateur de données
description: Cet article décrit hash_combine () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 1925d9b27382dd3a888e14243bfecad51d37db0d
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226701"
---
# <a name="hash_combine"></a>hash_combine()

Combine les valeurs de hachage d’au moins deux hachages.

**Syntaxe**

`hash_combine(`*H1* `,` *H2* [ `,` *H3* ...]`)`

**Arguments**

* *H1*: valeur de type long représentant la première valeur de hachage.
* *H2*: valeur de type long représentant la deuxième valeur de hachage.
* *hN*: valeur de type long représentant la nième valeur de hachage.

**Retourne**

Valeur de hachage combinée des scalaires données.

**Exemples**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|valeur1|valeur2|S1|H2|associés|
|---|---|---|---|---|
|Hello|World (Monde)|753694413698530628|1846988464401551951|-1440138333540407281|
