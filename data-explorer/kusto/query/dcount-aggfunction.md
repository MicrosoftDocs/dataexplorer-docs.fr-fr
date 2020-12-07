---
title: dcount() (fonction d’agrégation) – Azure Data Explorer
description: Cet article décrit dcount() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 947ab0af6a5aaa98bb07b08005b940fdf2ce6ae5
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513180"
---
# <a name="dcount-aggregation-function"></a>dcount() (fonction d’agrégation)

Retourne une estimation du nombre de valeurs distinctes prises par une expression scalaire dans le groupe de résumé.

> [!NOTE]
> La fonction d’agrégation `dcount()` est principalement utile pour estimer la cardinalité des jeux énormes. Elle privilégie la précision par rapport aux performances, et peut retourner un résultat variable d’une exécution à l’autre. L’ordre des entrées peut avoir une incidence sur sa sortie.

## <a name="syntax"></a>Syntaxe

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>Arguments

* *Expr* : expression scalaire dont les différentes valeurs doivent être comptées.
* *Précision* : littéral `int` facultatif qui définit l’exactitude d’estimation demandée. Pour connaître les valeurs prises en charge, voyez ci-dessous. Si la valeur n’est pas spécifiée, la valeur par défaut `1` est utilisée.

## <a name="returns"></a>Retours

Retourne une estimation du nombre de valeurs distinctes d’ *`Expr`* dans le groupe.

## <a name="example"></a>Exemple

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="D count":::

Obtient un nombre exact de valeurs distinctes de `V` regroupées par `G`.

```kusto
T | summarize by V, G | summarize count() by G
```

Ce calcul nécessite une grande quantité de mémoire interne, car les valeurs distinctes de `V` sont multipliées par le nombre de valeurs distinctes de `G`.
Cela peut entraîner des erreurs de mémoire ou des temps d’exécution importants. 
`dcount()`offre une alternative rapide et fiable :

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>Exactitude d’estimation

La fonction d’agrégation `dcount()` utilise une variante de l’algorithme [HyperLogLog (HLL)](https://en.wikipedia.org/wiki/HyperLogLog) qui effectue une estimation stochastique de la cardinalité définie. L’algorithme fournit un « bouton » qui peut être utilisé pour équilibrer la précision et la durée d’exécution par taille de mémoire :

|Précision|Erreur (%)|Nombre d’entrées   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0,8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0,28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> La colonne « Nombre d’entrées » indique le nombre de compteurs sur 1 octet dans l’implémentation de HLL.

L’algorithme inclut certaines dispositions pour effectuer un décompte parfait (zéro erreur) si la cardinalité définie est suffisamment petite :
* Lorsque le niveau d’exactitude est `1`, 1 000 valeurs sont retournées
* Lorsque le niveau d’exactitude est `2`, 8 000 valeurs sont retournées

La limite d’erreur est probabiliste, non théorique. La valeur est l’écart type de distribution des erreurs (sigma), et 99,7 % des estimations auront une erreur relative inférieure à 3 x sigma.

L’illustration suivante montre la fonction de distribution des probabilités de l’erreur d’estimation relative, en pourcentage, pour tous les paramètres d’exactitude pris en charge :

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="Distribution des erreurs hll":::
