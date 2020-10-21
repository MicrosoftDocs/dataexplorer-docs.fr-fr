---
title: ASIN ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ASIN () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: e23ddda4bbc2e165adfd810e1dc27b5ffd14edf4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246742"
---
# <a name="asin"></a>asin()

Retourne l’angle dont le sinus est le nombre spécifié (l’opération inverse de [`sin()`](sinfunction.md) ).

## <a name="syntax"></a>Syntaxe

`asin(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel dans la plage [-1, 1].

## <a name="returns"></a>Retours

* Valeur de l’arc sinus de `x`
* `null` Si `x` <-1 ou `x` > 1