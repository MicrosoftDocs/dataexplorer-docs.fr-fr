---
title: Fonctions de fenêtre-Azure Explorateur de données
description: Cet article décrit les fonctions des fenêtres dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: 067edde1e9a1adb519d4e485f816793e462de046
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253161"
---
# <a name="window-functions-overview"></a>Vue d’ensemble des fonctions de fenêtre

Les fonctions de fenêtre opèrent sur plusieurs lignes (enregistrements) dans un ensemble de lignes à la fois. Contrairement aux fonctions d’agrégation, les fonctions de fenêtre requièrent que les lignes de l’ensemble de lignes soient sérialisées (selon un ordre spécifique). Les fonctions de fenêtre peuvent dépendre de l’ordre pour déterminer le résultat.

Les fonctions de fenêtre ne peuvent être utilisées que sur des jeux sérialisés. Le moyen le plus simple de sérialiser un jeu de lignes consiste à utiliser l' [opérateur Serialize](./serializeoperator.md). Cet opérateur « fige » l’ordre des lignes de manière arbitraire. Si l’ordre des lignes sérialisées est sémantiquement important, utilisez l' [opérateur de tri](./sortoperator.md) pour forcer un ordre particulier.

Le processus de sérialisation est associé à un coût non négligeable. Par exemple, elle peut empêcher le parallélisme des requêtes dans de nombreux scénarios. Par conséquent, n’appliquez pas inutilement la sérialisation. Si nécessaire, réorganisez la requête pour effectuer la sérialisation sur le plus petit ensemble de lignes possible.

## <a name="serialized-row-set"></a>Ensemble de lignes sérialisé

Un ensemble de lignes arbitraire (par exemple, une table ou la sortie d’un opérateur tabulaire) peut être sérialisé de l’une des manières suivantes :

1. En triant l’ensemble de lignes. Pour obtenir la liste des opérateurs qui émettent des ensembles de lignes triés, voir ci-dessous.
2. À l’aide de l' [opérateur Serialize](./serializeoperator.md).

De nombreux opérateurs tabulaires sérialisent la sortie chaque fois que l’entrée est déjà sérialisée, même si l’opérateur ne garantit pas lui-même que le résultat est sérialisé. Par exemple, cette propriété est garantie pour l’opérateur [Extend](./extendoperator.md), l' [opérateur de projet](./projectoperator.md)et l' [opérateur WHERE](./whereoperator.md).

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>Opérateurs émettant des jeux de lignes sérialisés par tri

* [order, opérateur](./orderoperator.md)
* [opérateur sort](./sortoperator.md)
* [opérateur top](./topoperator.md)
* [top-hitters, opérateur](./tophittersoperator.md)
* [Opérateur top-nested](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>Opérateurs qui conservent la propriété de l’ensemble de lignes sérialisé

* [opérateur extend](./extendoperator.md)
* [mv-expand, opérateur](./mvexpandoperator.md)
* [opérateur parse](./parseoperator.md)
* [opérateur project](./projectoperator.md)
* [opérateur project-away](./projectawayoperator.md)
* [project-rename, opérateur](./projectrenameoperator.md)
* [opérateur take](./takeoperator.md)
* [opérateur where](./whereoperator.md)
