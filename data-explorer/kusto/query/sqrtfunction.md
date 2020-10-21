---
title: sqrt ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit sqrt () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 81b31d8a692a2f68cdb8dd68b5c5f1e75057d3b0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242512"
---
# <a name="sqrt"></a>sqrt()

Retourne la fonction racine carrée.  

## <a name="syntax"></a>Syntaxe

`sqrt(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel >= 0.

## <a name="returns"></a>Retours

* Nombre positif tel que `sqrt(x) * sqrt(x) == x`
* `null` si l’argument est négatif ou s’il ne peut pas être converti en une valeur `real`. 