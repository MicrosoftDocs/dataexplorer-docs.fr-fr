---
title: tohex() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit tohex() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 402a0923d4fe760e97fa6098ad955b6c24a5d5ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81506118"
---
# <a name="tohex"></a>tohex()

Convertit l’entrée en une chaîne hexadecimal.

```kusto
tohex(256) == '100'
tohex(-256) == 'ffffffffffffff00' // 64-bit 2's complement of -256
tohex(toint(-256), 8) == 'ffffff00' // 32-bit 2's complement of -256
tohex(256, 8) == '00000100'
tohex(256, 2) == '100' // Exceeds min length of 2, so min length is ignored.
```

**Syntaxe**

`tohex(`*Expr*`, [`` *MinLength*]`, )'

**Arguments**

* *Expr*: valeur int ou longue qui sera convertie en chaîne hex.  D’autres types ne sont pas pris en charge.
* *MinLength*: valeur numérique représentant le nombre de personnages de premier plan à inclure dans la sortie.  Les valeurs entre 1 et 16 sont soutenues, les valeurs supérieures à 16 seront tronquées à 16.  Si la corde est plus longue que minLength sans personnages principaux, puis minLength est effectivement ignoré.  Les nombres négatifs ne peuvent être représentés au minimum que par leur taille de données sous-jacente, donc pour un int (32 bits) la longueur minLength sera au moins 8, pour une longue (64 bits) il sera au minimum 16.

**Retourne**

Si la conversion est réussie, le résultat sera une valeur de chaîne.
Si la conversion n’est pas réussie, le résultat sera nul.