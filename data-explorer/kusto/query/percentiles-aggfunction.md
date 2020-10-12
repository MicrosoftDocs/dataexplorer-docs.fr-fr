---
title: centile (), centile ()-Azure Explorateur de données
description: Cet article décrit centile (), centile () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 0322dd6a8ba900fa4d55bea6b3568a5c42f61b52
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942367"
---
# <a name="percentile-percentiles-aggregation-function"></a>centile (), centile () (fonction d’agrégation)

Retourne une estimation pour le [centile le plus proche](#nearest-rank-percentile) de la population défini par `*Expr*` .
La précision dépend de la densité de population dans la région du centile. Cette fonction ne peut être utilisée que dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

* `percentiles()` est similaire `percentile()` à, mais calcule un nombre de valeurs de centile, ce qui est plus rapide que le calcul individuel de chaque centile.
* `percentilesw()` est similaire `percentilew()` à, mais calcule un nombre de valeurs de centile pondérées, ce qui est plus rapide que le calcul individuel de chaque centile.
* `percentilew()` et `percentilesw()` vous permettent de calculer des centile pondérés. Les centile pondérés calculent les centile donnés d’une manière « pondérée », en traitant chaque valeur comme si elle était répétée `weight` , dans l’entrée.

## <a name="syntax"></a>Syntaxe

résumer `percentile(` *expr* `,` *centile*`)`

résumer `percentiles(` *expr* `,` *Percentile1* [ `,` *Percentile2*]`)`

résumer `percentiles_array(` *expr* `,` *Percentile1* [ `,` *Percentile2*]`)`

résumer le `percentiles_array(` *Expr* `,` *tableau dynamique* expr`)`

résumer `percentilew(` *expr* `,` *WeightExpr* `,` *centile*`)`

résumer `percentilesw(` *expr* `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

résumer `percentilesw_array(` *expr* `,` *WeightExpr* `,` *Percentile1* [ `,` *Percentile2*]`)`

résumer le `percentilesw_array(` *Expr* `,` *WeightExpr* `,` *tableau dynamique* expr WeightExpr`)`

## <a name="arguments"></a>Arguments

* `*Expr*`: Expression qui sera utilisée pour le calcul de l’agrégation.
* `*WeightExpr*`: Expression qui sera utilisée comme pondération des valeurs pour le calcul de l’agrégation.
* `*Percentile*`: Constante double qui spécifie le centile.
* `*Dynamic array*`: liste de centiles dans un tableau dynamique de nombres entiers ou à virgule flottante.

## <a name="returns"></a>Retours

Retourne une estimation pour `*Expr*` les centiles spécifiés dans le groupe. 

## <a name="examples"></a>Exemples

La valeur de `Duration` est supérieure à 95% de l’échantillon défini et inférieure à 5% de l’échantillon.

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

Calculer simultanément 5, 50 (median) et 95.

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/percentiles-aggfunction/percentiles.png" alt-text="Tableau répertoriant les résultats, avec les colonnes du continent et les valeurs de durée des cinquième, Fiftieth et cinquième centile.":::

Les résultats montrent qu’en Europe, 5% des appels sont plus courts que 11.55 s, 50% des appels sont plus courts que 3 minutes, 18,46 secondes et 95% des appels sont plus courts que 40 minutes 48 secondes.

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>Centiles pondérés

Supposons que vous mesurez de façon répétée l’heure (durée) à laquelle une action est exécutée. Au lieu d’enregistrer chaque valeur de mesure, vous enregistrez chaque valeur de Duration, arrondie à 100 ms, et le nombre de fois où la valeur arrondie est apparue (BucketSize).

Utilisez `summarize percentilesw(Duration, BucketSize, ...)` pour calculer les centiles donnés d’une manière « pondérée ». Traitez chaque valeur de Duration comme si elle était répétée BucketSize fois dans l’entrée, sans avoir réellement besoin de matérialiser ces enregistrements.

## <a name="example"></a>Exemple

Un client a un ensemble de valeurs de latence en millisecondes : `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }` .

Pour réduire la bande passante et le stockage, effectuez une pré-agrégation pour les compartiments suivants : `{ 10, 20, 30, 40, 50, 100 }` . Comptez le nombre d’événements dans chaque compartiment pour générer le tableau suivant :

:::image type="content" source="images/percentiles-aggfunction/percentilesw-table.png" alt-text="Tableau répertoriant les résultats, avec les colonnes du continent et les valeurs de durée des cinquième, Fiftieth et cinquième centile.":::

Le tableau suivant affiche :
 * Huit événements dans le compartiment 10-ms (correspondant au sous-ensemble `{ 1, 1, 2, 2, 2, 5, 7, 7 }` )
 * Six événements dans le compartiment 20-ms (correspondant au sous-ensemble `{ 12, 12, 15, 15, 15, 18 }` )
 * Trois événements dans le compartiment 30-ms (correspondant au sous-ensemble `{ 21, 22, 26 }` )
 * Un événement dans le compartiment 40-ms (correspondant au sous-ensemble `{ 35 }` )

À ce stade, les données d’origine ne sont plus disponibles. Uniquement le nombre d’événements dans chaque compartiment. Pour calculer des centile à partir de ces données, utilisez la `percentilesw()` fonction.
Par exemple, pour les centile 50, 75 et 99,9, utilisez la requête suivante.

```kusto
datatable (ReqCount:long, LatencyBucket:long) 
[ 
    8, 10, 
    6, 20, 
    3, 30, 
    1, 40 
]
| summarize percentilesw(LatencyBucket, ReqCount, 50, 75, 99.9) 
```

Le résultat de la requête ci-dessus est le suivant :

:::image type="content" source="images/percentiles-aggfunction/percentilesw-result.png" alt-text="Tableau répertoriant les résultats, avec les colonnes du continent et les valeurs de durée des cinquième, Fiftieth et cinquième centile." border="false":::


La requête ci-dessus correspond à la fonction `percentiles(LatencyBucket, 50, 75, 99.9)` , si les données ont été développées au format suivant :

:::image type="content" source="images/percentiles-aggfunction/percentilesw-rawtable.png" alt-text="Tableau répertoriant les résultats, avec les colonnes du continent et les valeurs de durée des cinquième, Fiftieth et cinquième centile.":::

## <a name="getting-multiple-percentiles-in-an-array"></a>Obtention de plusieurs centiles dans un tableau

Plusieurs centiles peuvent être obtenus sous la forme d’un tableau dans une colonne dynamique unique, plutôt que dans plusieurs colonnes.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/percentiles-aggfunction/percentiles-array-result.png" alt-text="Tableau répertoriant les résultats, avec les colonnes du continent et les valeurs de durée des cinquième, Fiftieth et cinquième centile.":::

De même, les centile pondérés peuvent être retournés sous la forme d’un tableau dynamique à l’aide de `percentilesw_array` .

Les centile pour `percentiles_array` et `percentilesw_array` peuvent être spécifiés dans un tableau dynamique de nombres entiers ou à virgule flottante. Le tableau doit être constant, mais il ne doit pas nécessairement être un littéral.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>Centile le plus proche

*P*-th centile (0 < *P* <= 100) d’une liste de valeurs ordonnées, triées du moins au plus grand, est la plus petite valeur de la liste. Les% *p* pour cent des données sont inférieures ou égales à la valeur du *p*-ième centile ([à partir de l’article Wikipédia sur les centiles](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method)).

Définissez *0*-th centile pour être le plus petit membre de la population.

>[!NOTE]
> Compte tenu de la nature approximative du calcul, la valeur retournée réelle ne peut pas être un membre du remplissage.
> La définition de classement la plus proche signifie que *P*= 50 n’est pas conforme à la [définition interpolive de la médiane](https://en.wikipedia.org/wiki/Median). Lors de l’évaluation de l’importance de cette différence pour l’application spécifique, la taille du remplissage et une [erreur d’estimation](#estimation-error-in-percentiles) doivent être prises en compte.

## <a name="estimation-error-in-percentiles"></a>Erreur d’estimation dans les centiles

L’agrégation de centiles fournit une valeur approximative au moyen de [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf).

>[!NOTE]
> * Les limites de l’erreur d’estimation dépendent de la valeur du centile demandé. La meilleure précision se trouve aux deux extrémités de l’échelle [0.. 100]. Les centiles 0 et 100 sont les valeurs minimales et maximales exactes de la distribution. La précision diminue progressivement vers le milieu de l’échelle. C’est le pire au médian et est limité à 1%.
> * Les limites d’erreur sont observées sur le classement, et non sur la valeur. Supposons que centile (X, 50) a retourné la valeur XM. L’estimation garantit qu’au moins 49% et au plus 51% des valeurs de X sont inférieures ou égales à XM. Il n’existe aucune limite théorique quant à la différence entre XM et la valeur médiane réelle de X.
> * L’estimation peut parfois aboutir à une valeur précise, mais il n’existe aucune condition fiable à définir quand elle sera la casse.
