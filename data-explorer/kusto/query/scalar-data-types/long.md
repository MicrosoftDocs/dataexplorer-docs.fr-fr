---
title: Le type de données longues - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le type de données longue dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b8c51f216b8515e483daf64d9783210ff35ceef3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509705"
---
# <a name="the-long-data-type"></a>Le type de données longues

Le `long` type de données représente un intégré signé, 64 bits de large.

## <a name="long-literals"></a>longs littérals

Les littérals du `long` type de données peuvent être spécifiés dans la syntaxe suivante :

`long``(` *Valeur*`)`

Où *la valeur* peut prendre les formes suivantes :
* Un de plus ou des chiffres, auquel cas la valeur littérale est la représentation décimale de ces chiffres. Par exemple, `long(12)` est le `long`nombre douze de type .
* Le préfixe `0x` suivi d’un ou plusieurs chiffres Hex. Par exemple, `long(0xf)` équivaut à `long(15)`.
* Un signe`-`moins () suivi d’un ou plusieurs chiffres. Par exemple, `long(-1)` est le nombre `long`moins un de type .
* `null`, dans ce cas, il `long` s’agit de la valeur [nulle](null-values.md) du type de données. Ainsi, la valeur `long` nulle `long(null)`du type est .

Kusto prend également en `long` charge `long(` / `)` les littérals de type sans le préfixe/suffi pour les deux premiers formulaires seulement. Ainsi, `123` est un littéral `long` `0x123`de `-2` type , tel qu’est, mais **n’est pas** un littéral (il est actuellement interprété comme la fonction `-` unary appliquée à la littérale `2` de type long).
 
Pour convertir longtemps en chaîne hex - voir [tohex() fonction](../tohexfunction.md).