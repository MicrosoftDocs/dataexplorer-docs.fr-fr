---
title: anyif () (fonction d’agrégation)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit anyif () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 23285c0747e7fecbdce810536af195f72f27236f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349718"
---
# <a name="anyif-aggregation-function"></a>anyif () (fonction d’agrégation)

Sélectionne de façon arbitraire un enregistrement pour chaque groupe dans un [opérateur de synthèse](summarizeoperator.md), pour lequel le prédicat a la valeur « true ». La fonction retourne la valeur d’une expression sur chaque enregistrement de ce type.

## <a name="syntax"></a>Syntaxe

`summarize``anyif` `(` *Expr*, *prédicat*`)`

## <a name="arguments"></a>Arguments

* *Expr*: expression sur chaque enregistrement sélectionné à partir de l’entrée à retourner.
* *Predicate*: prédicat pour indiquer les enregistrements qui peuvent être pris en compte pour l’évaluation.

## <a name="returns"></a>Retourne

La `anyif` fonction d’agrégation retourne la valeur de l’expression calculée pour chaque enregistrement sélectionné de façon aléatoire dans chaque groupe de l’opérateur de synthèse. Seuls les enregistrements pour lesquels le *prédicat* retourne la valeur « true » peuvent être sélectionnés. Si le prédicat ne retourne pas la valeur « true », une valeur null est générée.

**Remarques**

Cette fonction est utile lorsque vous souhaitez obtenir un exemple de valeur d’une colonne par valeur de clé de groupe composée, selon un prédicat qui est « true ».

La fonction tente de retourner une valeur non NULL/non vide, si une telle valeur est présente.

## <a name="examples"></a>Exemples

Affichez un continent aléatoire avec un remplissage de 300 à 600 millions.

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="N’importe lequel 1":::
