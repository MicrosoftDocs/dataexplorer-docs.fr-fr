---
title: log ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit log () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 2b7c4f0ac50c35dba0a1d59540fdaafb0cda2bfd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241460"
---
# <a name="log"></a>log()

`log()` retourne la fonction de logarithme népérien.  

## <a name="syntax"></a>Syntaxe

`log(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel > 0.

## <a name="returns"></a>Retours

* Le logarithme népérien est le logarithme de base e : l’inverse de la fonction exponentielle naturelle (exp).
* `null` Si l’argument est négatif ou null ou ne peut pas être converti en `real` valeur. 

## <a name="see-also"></a>Voir aussi

* Pour les logarithmes courants (base 10), consultez [log10 ()](log10-function.md).
* Pour les logarithmes en base 2, consultez [Log2 ()](log2-function.md)