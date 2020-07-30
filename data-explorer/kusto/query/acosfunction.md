---
title: ACOS ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ACOS () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: d1daf36e85eec4c8543ba14be153a9d6069e1cd1
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349854"
---
# <a name="acos"></a>acos()

Retourne l’angle dont le cosinus est le nombre spécifié (l’opération inverse de [`cos()`](cosfunction.md) ).

## <a name="syntax"></a>Syntaxe

`acos(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel dans la plage [-1, 1].

## <a name="returns"></a>Retourne

* Valeur du cosinus d’arc de`x`
* `null`Si `x` <-1 ou `x` > 1