---
title: tohex ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit tohex () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6cc9beb5f5229505cf5ac40f95de6bafeb979f6f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350704"
---
# <a name="tohex"></a>tohex()

Convertit l’entrée en une chaîne hexadécimale.

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

## <a name="syntax"></a>Syntaxe

`tohex(`*Expr* `, [` , ` *MinLength*]` ) '

## <a name="arguments"></a>Arguments

* *Expr*: int ou valeur de type long qui sera convertie en une chaîne hexadécimale.  Les autres types ne sont pas pris en charge.
* *MinLength*: valeur numérique représentant le nombre de caractères de début à inclure dans la sortie.  Les valeurs comprises entre 1 et 16 sont prises en charge, les valeurs supérieures à 16 sont tronquées à 16.  Si la chaîne est plus longue que minLength sans caractères de début, alors minLength est effectivement ignoré.  Les nombres négatifs ne peuvent être représentés qu’au minimum par leur taille de données sous-jacente. par conséquent, pour un int (32 bits), le minLength sera au minimum 8, pour un long (64 bits), il sera au minimum 16.

## <a name="returns"></a>Retourne

Si la conversion réussit, le résultat sera une valeur de chaîne.
Si la conversion échoue, le résultat sera null.