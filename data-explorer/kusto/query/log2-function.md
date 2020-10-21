---
title: Log2 ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Log2 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: c5bab8e2f4106ddafc085fa35da424db60616a46
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243250"
---
# <a name="log2"></a>log2()

`log2()` retourne la fonction de logarithme en base 2.  

## <a name="syntax"></a>Syntaxe

`log2(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel > 0.

## <a name="returns"></a>Retours

* Le logarithme est le logarithme de base 2 : l’inverse de la fonction exponentielle (exp) avec la base 2.
* `null` Si l’argument est négatif ou null ou ne peut pas être converti en `real` valeur. 

## <a name="see-also"></a>Voir aussi

* Pour obtenir des logarithmes naturels (base-e), consultez [log ()](log-function.md).
* Pour les logarithmes courants (base 10), consultez [log10 ()](log10-function.md).