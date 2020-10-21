---
title: log10 ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit log10 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 978e40f3a2727b08dd404068ad364c7416361f66
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241410"
---
# <a name="log10"></a>log10()

`log10()` retourne la fonction de logarithme (base 10) courant.  

## <a name="syntax"></a>Syntaxe

`log10(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel > 0.

## <a name="returns"></a>Retours

* Le logarithme commun est le logarithme de base 10 : l’inverse de la fonction exponentielle (exp) avec base 10.
* `null` Si l’argument est négatif ou null ou ne peut pas être converti en `real` valeur. 

## <a name="see-also"></a>Voir aussi

* Pour obtenir des logarithmes naturels (base-e), consultez [log ()](log-function.md).
* Pour les logarithmes en base 2, consultez [Log2 ()](log2-function.md)