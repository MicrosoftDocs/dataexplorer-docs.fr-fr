---
title: sqrt() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit sqrt() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 660235a60893732288a551e1febd9b7b044b4f00
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507257"
---
# <a name="sqrt"></a>sqrt()

Retourne la fonction racine carrée.  

**Syntaxe**

`sqrt(`*X*`)`

**Arguments**

* *x*: Un nombre réel >0.

**Retourne**

* Nombre positif tel que `sqrt(x) * sqrt(x) == x`
* `null` si l’argument est négatif ou s’il ne peut pas être converti en une valeur `real`. 