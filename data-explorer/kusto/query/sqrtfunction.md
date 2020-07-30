---
title: sqrt ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit sqrt () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 76f5c8c5f8c1a0b9f685ae88df1ab624dc446150
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350959"
---
# <a name="sqrt"></a>sqrt()

Retourne la fonction racine carrée.  

## <a name="syntax"></a>Syntaxe

`sqrt(`*x*`)`

## <a name="arguments"></a>Arguments

* *x*: nombre réel >= 0.

## <a name="returns"></a>Retourne

* Nombre positif tel que `sqrt(x) * sqrt(x) == x`
* `null` si l’argument est négatif ou s’il ne peut pas être converti en une valeur `real`. 