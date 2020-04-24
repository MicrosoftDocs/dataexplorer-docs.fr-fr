---
title: log10() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit log10() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 9ccc83ff466d0414f793b7cfbbcf10d2ca169348
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513190"
---
# <a name="log10"></a>log10()

`log10()`retourne la fonction commune (base-10) logarithm.  

**Syntaxe**

`log10(`*X*`)`

**Arguments**

* *x*: Un vrai nombre > 0.

**Retourne**

* Le logarithme commun est le logarithme de base-10 : l’inverse de la fonction exponentielle (exp) avec la base 10.
* `null`si l’argument est négatif ou nul ou `real` ne peut pas être converti en valeur. 

**Voir aussi**

* Pour les logarithmes naturels (base-e), voir [logarithmes,)](log-function.md).
* Pour les logarithmes de base-2, voir [log2()](log2-function.md)