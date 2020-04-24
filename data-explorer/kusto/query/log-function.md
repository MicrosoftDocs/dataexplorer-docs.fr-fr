---
title: journal() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le journal () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 7bd3c4f5c6b262587cab2327486945f5d78aaf02
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513275"
---
# <a name="log"></a>log()

`log()`retourne la fonction de logarithm naturel.  

**Syntaxe**

`log(`*X*`)`

**Arguments**

* *x*: Un vrai nombre > 0.

**Retourne**

* Le logarithme naturel est le logarithme de base-e : l’inverse de la fonction exponentielle naturelle (exp).
* `null`si l’argument est négatif ou nul ou `real` ne peut pas être converti en valeur. 

**Voir aussi**

* Pour les logarithmes communs (base-10), voir [log10()](log10-function.md).
* Pour les logarithmes de base-2, voir [log2()](log2-function.md)