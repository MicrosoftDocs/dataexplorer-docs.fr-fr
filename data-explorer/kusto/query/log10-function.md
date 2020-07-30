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
ms.openlocfilehash: 9db15900ea258d42e377f47de9ad12eecf52386d
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347117"
---
# <a name="log10"></a>log10()

`log10()`retourne la fonction de logarithme (base 10) courant.  

## <a name="syntax"></a>Syntaxe

`log10(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel > 0.

## <a name="returns"></a>Retourne

* Le logarithme commun est le logarithme de base 10 : l’inverse de la fonction exponentielle (exp) avec base 10.
* `null`Si l’argument est négatif ou null ou ne peut pas être converti en `real` valeur. 

**Voir aussi**

* Pour obtenir des logarithmes naturels (base-e), consultez [log ()](log-function.md).
* Pour les logarithmes en base 2, consultez [Log2 ()](log2-function.md)