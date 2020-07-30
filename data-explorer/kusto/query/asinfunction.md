---
title: ASIN ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ASIN () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 360a936d136cd01760175cdf5b5da2718d0cfd91
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349497"
---
# <a name="asin"></a>asin()

Retourne l’angle dont le sinus est le nombre spécifié (l’opération inverse de [`sin()`](sinfunction.md) ).

## <a name="syntax"></a>Syntaxe

`asin(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel dans la plage [-1, 1].

## <a name="returns"></a>Retours

* Valeur de l’arc sinus de`x`
* `null`Si `x` <-1 ou `x` > 1