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
ms.openlocfilehash: 283cd1427dedb04b036d7cb23e650d8f0651a58c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347134"
---
# <a name="log"></a>log()

`log()`retourne la fonction de logarithme népérien.  

## <a name="syntax"></a>Syntaxe

`log(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel > 0.

## <a name="returns"></a>Retourne

* Le logarithme népérien est le logarithme de base e : l’inverse de la fonction exponentielle naturelle (exp).
* `null`Si l’argument est négatif ou null ou ne peut pas être converti en `real` valeur. 

**Voir aussi**

* Pour les logarithmes courants (base 10), consultez [log10 ()](log10-function.md).
* Pour les logarithmes en base 2, consultez [Log2 ()](log2-function.md)