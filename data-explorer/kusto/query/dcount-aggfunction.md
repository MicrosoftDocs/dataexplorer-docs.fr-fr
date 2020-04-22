---
title: dcount() (fonction d’agrégation) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit dcount() (fonction d’agrégation) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abd474d1ef06a71e3971df18c7ba65904b34ee06
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663829"
---
# <a name="dcount-aggregation-function"></a>dcount)) (fonction d’agrégation)

Renvoie une estimation du nombre de valeurs distinctes prises par une expression scalaire dans le groupe sommaire.

**Syntaxe**

... `|` `,` *Accuracy*`)` *Expr* Expr [ Précision ] ... `summarize` `dcount` `(`

**Arguments**

* *Expr*: Une expression scalaire dont les valeurs distinctes doivent être comptées.
* *Précision*: `int` Un littéral optionnel qui définit l’exactitude d’estimation demandée. Voir ci-dessous pour les valeurs prises en charge. Si elle n’est pas `1` précisée, la valeur par défaut est utilisée.

**Retourne**

Retourne une estimation du nombre de valeurs distinctes de *Expr* dans le groupe.

**Exemple**

```kusto
PageViewLog | summarize countries=dcount(country) by continent
```

![texte de remplacement](./images/aggregations/dcount.png "dcount")

**Remarques**

La `dcount()` fonction d’agrégation est principalement utile pour estimer la cardinalité des ensembles énormes. Il échange les performances pour l’exactitude, et peut donc retourner un résultat qui varie entre les exécutions (l’ordre des entrées peut avoir un effet sur sa sortie).

Pour obtenir un compte précis `V` des `G`valeurs distinctes de groupé par :

```kusto
T | summarize by V, G | summarize count() by G
```

Ce calcul exigera beaucoup de `V` mémoire interne puisque les valeurs `G`distinctes de sont multipliées par le nombre de valeurs distinctes de ; Par conséquent, il peut entraîner des erreurs de mémoire ou de grands temps d’exécution. `dcount()`offre une alternative rapide et fiable :

```kusto
T | summarize dcount(B) by G | count
```

## <a name="estimation-accuracy"></a>Précision d’estimation

La `dcount()` fonction globale utilise une variante de [l’algorithme HyperLogLog (HLL),](https://en.wikipedia.org/wiki/HyperLogLog)qui fait une estimation stochastique de la cardinalité définie. L’algorithme fournit un "knob" qui peut être utilisé pour équilibrer la précision et le temps d’exécution / taille de la mémoire:

|Précision|Erreur (%)|Nombre d’entrées   |
|--------|---------|--------------|
|       0|      1.6|2<sup>12</sup>|
|       1|      0,8|2<sup>14</sup>|
|       2|      0.4|2<sup>16</sup>|
|       3|     0.28|2<sup>17</sup>|
|       4|      0.2|2<sup>18</sup>|

> [!NOTE]
> La colonne « nombre d’entrées » est le nombre de compteurs 1-byte dans la mise en œuvre HLL.

L’algorithme comprend quelques dispositions pour faire un compte parfait (zéro erreur) si la cardinalité `1`définie est assez petite (1000 valeurs lorsque le niveau de précision est , et 8000 valeurs si le niveau de précision est `2`).

L’erreur liée est probabiliste, pas une limite théorique. La valeur est l’écart standard de la distribution d’erreurs (le sigma), et 99,7% des estimations auront une erreur relative de moins de 3 fois sigma.

Ce qui suit décrit la fonction de distribution de probabilité de l’erreur d’estimation relative (en pourcentages) pour tous les paramètres de précision pris en charge :

:::image type="content" border="false" source="images/aggregations/hll-error-distribution.png" alt-text="distribution d’erreurs hll":::
