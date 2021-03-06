---
title: hash_combine ()-Azure Explorateur de données
description: Cet article décrit hash_combine () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 91a178ded007ff75d96ed73f864276aa7d193d8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244773"
---
# <a name="hash_combine"></a>hash_combine()

Combine les valeurs de hachage d’au moins deux hachages.

## <a name="syntax"></a>Syntaxe

`hash_combine(`*H1* `,` *H2* [ `,` *H3* ...]`)`

## <a name="arguments"></a>Arguments

* *H1*: valeur de type long représentant la première valeur de hachage.
* *H2*: valeur de type long représentant la deuxième valeur de hachage.
* *hN*: valeur de type long représentant la nième valeur de hachage.

## <a name="returns"></a>Retours

Valeur de hachage combinée des scalaires données.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|valeur1|valeur2|S1|H2|associés|
|---|---|---|---|---|
|Hello|World (Monde)|753694413698530628|1846988464401551951|-1440138333540407281|
