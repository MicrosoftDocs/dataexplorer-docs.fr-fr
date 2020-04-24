---
title: Opérateurs logiques (binaires) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les opérateurs logiques (binaires) d’Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 53505067b93234aa89849e1c66fe2f77a58e0f26
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513088"
---
# <a name="logical-binary-operators"></a>Opérateurs logiques (binaires)

Les opérateurs logiques suivants sont `bool` pris en charge entre deux valeurs du type :

> [!NOTE]
> Ces opérateurs logiques sont parfois appelés opérateurs Boolean, et parfois comme des opérateurs binaires. Les noms sont tous synonymes.

|Nom de l'opérateur|Syntaxe|Signification|
|-------------|------|-------|
|Égalité     |`==`  |Les `true` rendements si les deux opérandes ne sont pas nuls et égaux l’un à l’autre. Sinon, valeur `false`.|
|Inégalité   |`!=`  |Les `true` rendements si l’un (ou les deux) des opérands sont nuls, ou ils ne sont pas égaux les uns aux autres. Sinon, valeur `true`.|
|Logique et  |`and` |Rendements `true` si les `true`deux opérands sont .|
|Logique ou   |`or`  |Rendements `true` si l’un `true`des opérands est, indépendamment de l’autre opérande.|

> [!NOTE]
> En raison du comportement de `bool(null)`la valeur nulle Boolean , deux valeurs nulles `bool(null) == bool(null)` Boolean ne sont ni égales ni non égales (en d’autres termes, et `bool(null) != bool(null)` les deux donnent la valeur `false`).
>
> D’autre `and` / `or` part, traiter la valeur `false`nulle `bool(null) or true` `true`comme `bool(null) and true` équivalent `false`à , est donc , et est .