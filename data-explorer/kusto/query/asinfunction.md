---
title: asin() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit asin() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 5db112daeba59dd841b02df8ba1a41185654ad6a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518545"
---
# <a name="asin"></a>asin()

Retourne l’angle dont le sinus est [`sin()`](sinfunction.md)le nombre spécifié (le fonctionnement inverse de ) .

**Syntaxe**

`asin(`*X*`)`

**Arguments**

* *x*: Un vrai nombre dans la gamme [-1, 1].

**Retourne**

* La valeur du sinus d’arc de`x`
* `null`si `x` < -1 ou `x` > 1