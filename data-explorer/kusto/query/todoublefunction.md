---
title: ToDouble ()/TOREAL ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ToDouble ()/TOREAL () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9a1a18ffdfc28d0487464202baa759acccd3e40e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250470"
---
# <a name="todouble-toreal"></a>todouble(), toreal()

Convertit l’entrée en une valeur de type `real` . ( `todouble()` et `toreal()` sont des synonymes).

```kusto
toreal("123.4") == 123.4
```

> [!NOTE]
> Préférez l’utilisation de la [double () ou réelle ()](./scalar-data-types/real.md) si possible.

## <a name="syntax"></a>Syntaxe

`toreal(`*Expr* `)` 
 Expr `todouble(` *Expr*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression dont la valeur sera convertie en une valeur de type `real` .

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat est une valeur de type `real` .
Si la conversion échoue, le résultat est la valeur `real(null)` .
