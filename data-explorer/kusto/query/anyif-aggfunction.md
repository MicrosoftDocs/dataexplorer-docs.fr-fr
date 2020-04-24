---
title: anyif() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit anyif() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 813df821bad1b7e57315dad9bcd7b1387a2cd678
ms.sourcegitcommit: 29018b3db4ea7d015b1afa65d49ecf918cdff3d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82030075"
---
# <a name="anyif-aggregation-function"></a>anyif() (fonction d’agrégation)

Choisit arbitrairement un enregistrement pour chaque groupe dans un [opérateur de résumé](summarizeoperator.md) pour lequel le prédicat est vrai, et retourne la valeur d’une expression sur chaque enregistrement.

**Syntaxe**

`summarize`Expr , *Predicate* )' *Expr* `anyif` `(`

**Arguments**

* *Expr*: Une expression sur chaque enregistrement sélectionné à partir de l’entrée pour revenir.
* *Prédication*: Prédicez pour indiquer quels documents peuvent être pris en considération pour évaluation.

**Retourne**

La `anyif` fonction d’agrégation renvoie la valeur de l’expression calculée pour chacun des enregistrements choisis au hasard à partir de chaque groupe de l’opérateur de résumé. Seuls les enregistrements pour lesquels les rendements *predicate* sont vrais peuvent être sélectionnés (si le prédicat ne retourne pas vrai, une valeur nulle est produite).

**Remarques**

Cette fonction est utile lorsque vous voulez obtenir un échantillon de valeur d’une colonne par valeur de la clé du groupe composé, sous réserve que certains prédicent être vrai.

La fonction tente de retourner une valeur non nulle/non vide, si cette valeur est présente.

**Exemples**

Afficher le continent aléatoire qui a une population de 300 millions à 600 millions:

```kusto
Continents | summarize anyif(Continent, Population between (300000000 .. 600000000))
```

:::image type="content" source="images/aggfunction/any1.png" alt-text="N’importe quel 1":::