---
title: ACOS ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ACOS () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 486be5487071fa281765de410fe4e5be1ce62a38
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253113"
---
# <a name="acos"></a>acos()

Retourne l’angle dont le cosinus est le nombre spécifié (l’opération inverse de [`cos()`](cosfunction.md) ).

## <a name="syntax"></a>Syntaxe

`acos(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel dans la plage [-1, 1].

## <a name="returns"></a>Retours

* Valeur du cosinus d’arc de `x`
* `null` Si `x` <-1 ou `x` > 1