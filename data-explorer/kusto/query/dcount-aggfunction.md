---
title: DCount () (fonction d’agrégation)-Azure Explorateur de données
description: Cet article décrit DCount () (fonction d’agrégation) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2fc8ee7e8c7ab3ce372d786ec87edf55265e1249
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348443"
---
# <a name="dcount-aggregation-function"></a>DCount () (fonction d’agrégation)

Retourne une estimation du nombre de valeurs distinctes prises par une expression scalaire dans le groupe de résumé.

## <a name="syntax"></a>Syntaxe

... `|` `summarize` `dcount` `(`*`Expr`*[, *`Accuracy`*]`)` ...

## <a name="arguments"></a>Arguments

* *Expr*: expression scalaire dont les valeurs distinctes doivent être comptées.
* *Précision*: un `int` littéral facultatif qui définit la précision d’estimation demandée. Voir ci-dessous pour connaître les valeurs prises en charge. S’il n’est pas spécifié, la valeur par défaut `1` est utilisée.

## <a name="returns"></a>Retourne

Retourne une estimation du nombre de valeurs distinctes de *`Expr`* dans le groupe.

## <a name="example"></a>Exemple

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

:::image type="content" source="images/dcount-aggfunction/dcount.png" alt-text="Nombre D":::

**Remarques**

La `dcount()` fonction d’agrégation est principalement utile pour estimer la cardinalité des jeux énormes. Elle génère des performances plus précises et peut retourner un résultat qui varie entre les exécutions. L’ordre des entrées peut avoir un effet sur sa sortie.

Obtient un nombre exact de valeurs distinctes `V` groupées par `G` .

```kusto
T | summarize by V, G | summarize count() by G
```

Ce calcul nécessite une grande quantité de mémoire interne, car les valeurs distinctes de `V` sont multipliées par le nombre de valeurs distinctes de `G` .
Cela peut entraîner des erreurs de mémoire ou des temps d’exécution importants. 
`dcount()`offre une alternative rapide et fiable :

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>Précision de l’estimation

La `dcount()` fonction Aggregate utilise une variante de l' [algorithme HYPERLOGLOG (HLL)](https://en.wikipedia.org/wiki/HyperLogLog), qui effectue une estimation stochastique de la cardinalité Set. L’algorithme fournit un « bouton » qui peut être utilisé pour équilibrer la précision et la durée d’exécution par taille de mémoire :

|Précision|Erreur (%)|Nombre d’entrées   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0,8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0,28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> La colonne « nombre d’entrées » est le nombre de compteurs sur 1 octet dans l’implémentation de HLL.

L’algorithme comprend certaines dispositions pour faire un compte parfait (zéro erreur), si la cardinalité définie est suffisamment petite :
* Lorsque le niveau de précision est `1` , 1000 valeurs sont retournées
* Lorsque le niveau de précision est `2` , 8000 valeurs sont retournées

L’erreur liée est probabiliste et non une limite théorique. La valeur est l’écart type de la distribution des erreurs (Sigma) et 99,7% des estimations auront une erreur relative de moins de 3 x Sigma.

L’illustration suivante montre la fonction de distribution de probabilité de l’erreur d’estimation relative, en pourcentage, pour tous les paramètres de précision pris en charge :

:::image type="content" border="false" source="images/dcount-aggfunction/hll-error-distribution.png" alt-text="distribution des erreurs HLL":::
