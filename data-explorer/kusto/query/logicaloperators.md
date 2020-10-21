---
title: Opérateurs logiques (binaires)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les opérateurs logiques (binaires) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/14/2018
ms.openlocfilehash: 6d1fcf7b9786951fedbdbf45d9e2c20a765452dd
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248509"
---
# <a name="logical-binary-operators"></a>Opérateurs logiques (binaires)

Les opérateurs logiques suivants sont pris en charge entre deux valeurs de `bool` type :

> [!NOTE]
> Ces opérateurs logiques sont parfois appelés opérateurs booléens, et parfois comme des opérateurs binaires. Les noms sont tous des synonymes.

|Nom de l'opérateur|Syntaxe|Signification|
|-------------|------|-------|
|Égalité     |`==`  |Produit `true` si les deux opérandes sont non null et égaux les uns des autres. Sinon, `false`.|
|Inégalité   |`!=`  |Produit `true` si l’un des opérandes (ou les deux) a la valeur null, ou s’ils ne sont pas égaux l’un à l’autre. Sinon, `true`.|
|ET logique  |`and` |Produit `true` si les deux opérandes sont `true` .|
|OU logique   |`or`  |Produit `true` si l’un des opérandes est `true` , indépendamment de l’autre opérande.|

> [!NOTE]
> En raison du comportement de la valeur booléenne null `bool(null)` , deux valeurs booléennes NULL ne sont ni égales ni non égales (en d’autres termes, `bool(null) == bool(null)` et `bool(null) != bool(null)` produisent toutes les deux la valeur `false` ).
>
> En revanche, `and` / `or` traitez la valeur NULL comme équivalent à `false` , donc `bool(null) or true` `true` , et `bool(null) and true` est `false` .