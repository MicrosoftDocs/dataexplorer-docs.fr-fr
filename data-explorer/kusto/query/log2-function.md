---
title: log2() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le journal2() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 41e9a1457f97fa04a4daa54e1929f27d8a448ae3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513122"
---
# <a name="log2"></a>log2()

`log2()`retourne la fonction de logarithme de base-2.  

**Syntaxe**

`log2(`*X*`)`

**Arguments**

* *x*: Un vrai nombre > 0.

**Retourne**

* Le logarithme est le logarithme de base-2 : l’inverse de la fonction exponentielle (exp) avec la base 2.
* `null`si l’argument est négatif ou nul ou `real` ne peut pas être converti en valeur. 

**Voir aussi**

* Pour les logarithmes naturels (base-e), voir [logarithmes,)](log-function.md).
* Pour les logarithmes communs (base-10), voir [log10()](log10-function.md).