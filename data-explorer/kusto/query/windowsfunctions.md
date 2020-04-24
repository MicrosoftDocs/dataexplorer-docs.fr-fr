---
title: Fonctions de fenêtre - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les fonctions de fenêtre dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/20/2019
ms.openlocfilehash: ab4f6da2478ba4de81b2034c1cb07458daa80bd0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504248"
---
# <a name="window-functions"></a>Fonctions Windows

Les fonctions de fenêtre fonctionnent sur plusieurs rangées (enregistrements) dans une rangée définie à la fois.
Contrairement aux fonctions d’agrégation, ils exigent que les lignes de l’ensemble de rangée soient **sérialisées** (ont un ordre spécifique pour eux), car les fonctions de fenêtre peuvent dépendre de l’ordre pour déterminer le résultat.

Les fonctions de fenêtre ne peuvent pas être utilisées sur des ensembles de ligne qui ne sont pas sérialisés, et donneront une erreur lorsqu’elles sont utilisées dans un tel contexte par une requête. La façon la plus simple de sérialiser un ensemble de lignes est d’utiliser [l’opérateur de sérialisation](./serializeoperator.md), qui « gèle » simplement l’ordre des rangées (d’une manière arbitraire non spécifiée).
Si l’ordre des lignes sérialisées est d’une importance sémantique, on peut utiliser [l’opérateur](./sortoperator.md) de tri pour forcer une commande particulière.

Le processus de sérialisation a un coût non négligeable qui lui est associé. Par exemple, il pourrait empêcher le parallélisme de requête dans beaucoup de scénarios. Par conséquent, il est fortement recommandé que la sérialisation ne soit pas appliquée inutilement, et que si nécessaire, la requête soit réarrangée afin que la sérialisation soit effectuée sur la plus petite rangée possible.

## <a name="serialized-row-set"></a>Ensemble de rangées sérialisées

Un ensemble de rangées arbitraires (comme une table ou la sortie d’un opérateur tabulaire) peut être sérialisé de l’une des façons suivantes :

1. En triant l’ensemble de rangées. Voir ci-dessous pour une liste d’opérateurs qui émettent des ensembles de lignes triés.
2. En utilisant [l’opérateur de sérialisation](./serializeoperator.md).

Notez que de nombreux opérateurs tabulaires, tandis qu’en eux-mêmes, ils ne garantissent pas que leur résultat est sérialisé, ont la propriété que si l’entrée est sérialisée, est donc la sortie. Par exemple, cette propriété est garantie pour [l’opérateur d’extension,](./extendoperator.md)l’opérateur du [projet,](./projectoperator.md)et [l’opérateur où](./whereoperator.md).

## <a name="operators-that-emit-serialized-row-sets-by-sorting"></a>Opérateurs qui émettent des ensembles de lignes sérialisés en triant

* [order, opérateur](./orderoperator.md)
* [opérateur sort](./sortoperator.md)
* [opérateur supérieur](./topoperator.md)
* [top-hitters, opérateur](./tophittersoperator.md)
* [Opérateur top-nested](./topnestedoperator.md)

## <a name="operators-that-preserve-the-serialized-row-set-property"></a>Opérateurs qui préservent la propriété sérialisée ensemble de rangée

* [opérateur extend](./extendoperator.md)
* [opérateur mv-expand](./mvexpandoperator.md)
* [opérateur parse](./parseoperator.md)
* [opérateur project](./projectoperator.md)
* [opérateur project-away](./projectawayoperator.md)
* [project-rename, opérateur](./projectrenameoperator.md)
* [opérateur take](./takeoperator.md)
* [où l’opérateur](./whereoperator.md)