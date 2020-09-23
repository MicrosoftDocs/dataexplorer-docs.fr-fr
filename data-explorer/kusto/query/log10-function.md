---
title: log10 ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit log10 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: 62813f5680e89c2bca0a6ec547fd7055ddc0e414
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103190"
---
# <a name="log10"></a>log10()

`log10()` retourne la fonction de logarithme (base 10) courant.  

## <a name="syntax"></a>Syntaxe

`log10(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel > 0.

## <a name="returns"></a>retourne :

* Le logarithme commun est le logarithme de base 10 : l’inverse de la fonction exponentielle (exp) avec base 10.
* `null` Si l’argument est négatif ou null ou ne peut pas être converti en `real` valeur. 

## <a name="see-also"></a>Voir aussi

* Pour obtenir des logarithmes naturels (base-e), consultez [log ()](log-function.md).
* Pour les logarithmes en base 2, consultez [Log2 ()](log2-function.md)