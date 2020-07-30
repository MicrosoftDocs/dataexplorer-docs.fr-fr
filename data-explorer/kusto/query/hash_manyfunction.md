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
ms.openlocfilehash: 3d0f389264d078d2b55ac06214bb3b820fcf7f13
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347593"
---
# <a name="hash_many"></a>hash_many()

Retourne une valeur de hachage combinée de plusieurs valeurs.

## <a name="syntax"></a>Syntaxe

`hash_many(`*S1* `,` *S2* [ `,` *S3* ...]`)`

## <a name="arguments"></a>Arguments

* *S1*, *S2*,..., *sn*: valeurs d’entrée qui seront hachées ensemble.

## <a name="returns"></a>Retours

Valeur de hachage combinée des scalaires données.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend combined = hash_many(value1, value2)
```

|valeur1|valeur2|associés|
|---|---|---|
|Hello|World (Monde)|-1440138333540407281|
