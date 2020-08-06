---
title: ToDouble ()/TOREAL ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ToDouble ()/TOREAL () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8e93e86814adf2789d01e03173196468f085b7c2
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87804098"
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
