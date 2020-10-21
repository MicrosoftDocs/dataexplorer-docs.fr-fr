---
title: Sign ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Sign () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: db6757e8c3ca871036cba1c21c1a20c3486b9249
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250004"
---
# <a name="sign"></a>sign()

Signe d’une expression numérique

## <a name="syntax"></a>Syntaxe

`sign(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>Retours

* Signe positif (+ 1), zéro (0) ou négatif (-1) de l’expression spécifiée. 

## <a name="examples"></a>Exemples

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|