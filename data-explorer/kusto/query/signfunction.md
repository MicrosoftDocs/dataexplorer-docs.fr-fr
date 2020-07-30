---
title: Sign ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Sign () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8d6761cc2ffa9a8c28151c00720bbd1340a56875
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351078"
---
# <a name="sign"></a>sign()

Signe d’une expression numérique

## <a name="syntax"></a>Syntaxe

`sign(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel.

## <a name="returns"></a>Retourne

* Signe positif (+ 1), zéro (0) ou négatif (-1) de l’expression spécifiée. 

## <a name="examples"></a>Exemples

```kusto
print s1 = sign(-42), s2 = sign(0), s3 = sign(11.2)

```

|s1|s2|s3|
|---|---|---|
|-1|0|1|