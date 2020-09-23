---
title: log ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit log () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: e81266bf43da93d2b36f0be5846e5d74f3157c7f
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103227"
---
# <a name="log"></a>log()

`log()` retourne la fonction de logarithme népérien.  

## <a name="syntax"></a>Syntaxe

`log(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel > 0.

## <a name="returns"></a>retourne :

* Le logarithme népérien est le logarithme de base e : l’inverse de la fonction exponentielle naturelle (exp).
* `null` Si l’argument est négatif ou null ou ne peut pas être converti en `real` valeur. 

## <a name="see-also"></a>Voir aussi

* Pour les logarithmes courants (base 10), consultez [log10 ()](log10-function.md).
* Pour les logarithmes en base 2, consultez [Log2 ()](log2-function.md)