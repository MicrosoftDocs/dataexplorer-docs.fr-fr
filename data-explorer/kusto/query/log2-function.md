---
title: Log2 ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Log2 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/11/2019
ms.openlocfilehash: a404dfba70f3a624acc08e70ee4b24935d1d7135
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347100"
---
# <a name="log2"></a>log2()

`log2()`retourne la fonction de logarithme en base 2.  

## <a name="syntax"></a>Syntaxe

`log2(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel > 0.

## <a name="returns"></a>Retourne

* Le logarithme est le logarithme de base 2 : l’inverse de la fonction exponentielle (exp) avec la base 2.
* `null`Si l’argument est négatif ou null ou ne peut pas être converti en `real` valeur. 

**Voir aussi**

* Pour obtenir des logarithmes naturels (base-e), consultez [log ()](log-function.md).
* Pour les logarithmes courants (base 10), consultez [log10 ()](log10-function.md).