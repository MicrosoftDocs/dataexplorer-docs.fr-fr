---
title: acos() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit acos() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: aa33714d57b319ba5a775385e8ee7d232addfe9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519327"
---
# <a name="acos"></a>acos()

Retourne l’angle dont la cosine est [`cos()`](cosfunction.md)le nombre spécifié (le fonctionnement inverse de ) .

**Syntaxe**

`acos(`*X*`)`

**Arguments**

* *x*: Un vrai nombre dans la gamme [-1, 1].

**Retourne**

* La valeur de l’arc cosine de`x`
* `null`si `x` < -1 ou `x` > 1