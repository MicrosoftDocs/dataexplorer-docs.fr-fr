---
title: percentiles(), percentiles () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit percentile (), percentiles () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: ecbb56305cfc43033ca172071f48b25768de6d2f
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663658"
---
# <a name="percentile-percentiles"></a>percentiles(), percentiles ()

Renvoie une estimation pour le [percentile](#nearest-rank-percentile) le plus proche spécifié de la population défini par *Expr*. La précision dépend de la densité de population dans la région du centile. Cette fonction ne peut être utilisée que dans le contexte de l’agrégation [à](summarizeoperator.md) l’intérieur

* `percentiles()`est `percentile()`comme , mais calcule un certain nombre de valeurs percentile (qui est plus rapide que le calcul de chaque percentile individuellement).
* `percentilesw()`est `percentilew()`comme , mais calcule un certain nombre de valeurs percentile pondérées (qui est plus rapide que le calcul de chaque percentile individuellement).
* `percentilew()`et `percentilesw()` permet de calculer les percentiles pondérés. Les percentiles pondérés calculent les percentiles donnés d’une manière `Weight` « pondérée » - traitant chaque valeur comme si elle était répétée fois dans l’entrée.

**Syntaxe**

résumer `percentile(` *Expr* `,` *Percentile*`)`

résumé `percentiles(` *Expr* `,` *Percentile1* [`,` *Percentile2*]`)`

résumé `percentiles_array(` *Expr* `,` *Percentile1* [`,` *Percentile2*]`)`

résumer `percentiles_array(`le tableau *Expr* `,` *Dynamic*`)`

résumer `percentilew(` *Expr* `,` *WeightExpr* `,` *Percentile*`)`

résumé `percentilesw(` *Expr* `,` *WeightExpr* `,` *Percentile1* [`,` *Percentile2*]`)`

résumé `percentilesw_array(` *Expr* `,` *WeightExpr* `,` *Percentile1* [`,` *Percentile2*]`)`

résumer `percentilesw_array(` *Expr* `,` *WeightExpr* `,` Dynamic *array*`)`

**Arguments**

* *Expr*: Expression qui sera utilisée pour le calcul de l’agrégation.
* *WeightExpr*: Expression qui sera utilisée comme poids des valeurs pour le calcul de l’agrégation.
* *Percentile* est une double constante qui spécifie le percentile.
* *Tableau dynamique*: liste des percentiles dans un tableau dynamique de nombres de points integers ou flottants.

**Retourne**

Retourne une estimation pour *Expr* des percentiles spécifiés dans le groupe. 

**Exemples**

La valeur `Duration` de ce montant est supérieure à 95 % de l’ensemble d’échantillons et inférieure à 5 % de l’ensemble d’échantillons :

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

Calculez simultanément 5, 50 (médiane) et 95 :

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/aggregations/percentiles.png" alt-text="Percentiles":::

Les résultats montrent qu’en Europe, 5% des appels sont plus courts que 11,55, 50% des appels sont plus courts que 3 minutes, 18,46 secondes, et 95% des appels sont plus courts que 40 minutes 48 secondes.

Calculer plusieurs statistiques :

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>Centiles pondérés

Supposons que vous mesurez le temps (Durée) il faut une action pour terminer encore et encore. Au lieu d’enregistrer toutes les valeurs de la mesure, vous les condensez en enregistrant chaque valeur de la durée, arrondie à 100 msec et combien de fois cette valeur arrondie est apparue (BucketSize).

Vous pouvez `summarize percentilesw(Duration, BucketSize, ...)` utiliser pour calculer les percentiles donnés d’une manière « pondérée » - traiter chaque valeur de la durée comme si elle a été répétée BucketSize fois dans l’entrée, sans réellement avoir besoin de matérialiser ces enregistrements.

Exemple : Un client a un ensemble de `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`valeurs de latence en millisecondes : .

Pour réduire la bande passante et le stockage, effectuer la `{ 10, 20, 30, 40, 50, 100 }`pré-agrégation aux seaux suivants: , et compter le nombre d’événements dans chaque seau, qui donne la table Suivante Kusto:

:::image type="content" source="images/aggregations/percentilesw-table.png" alt-text="Table percentilesw":::

Qui peut être lu comme:
 * 8 épreuves dans le seau de 10ms (correspondant au sous-ensemble `{ 1, 1, 2, 2, 2, 5, 7, 7 }`)
 * 6 épreuves dans le seau de 20ms (correspondant au sous-ensemble `{ 12, 12, 15, 15, 15, 18 }`)
 * 3 épreuves dans le seau de 30ms (correspondant au sous-ensemble `{ 21, 22, 26 }`)
 * 1 épreuve dans le seau de 40ms (correspondant au sous-ensemble `{ 35 }`)

À ce stade, les données d’origine ne sont plus disponibles, et tout ce que nous avons est le nombre d’événements dans chaque seau. Pour calculer les percentiles à partir `percentilesw()` de ces données, utilisez la fonction. Par exemple, pour les 50, 75 et 99,9 percentiles, utilisez la requête suivante : 

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

Le résultat de la requête ci-dessus est :

:::image type="content" source="images/aggregations/percentilesw-result.PNG" alt-text="Résultat de Percentilesw":::

Notez que la requête ci-dessus correspond à la fonction `percentiles(LatencyBucket, 50, 75, 99.9)` si les données ont été dépensées au formulaire suivant :

:::image type="content" source="images/aggregations/percentilesw-rawtable.png" alt-text="Table crue de Percentilesw":::

## <a name="getting-multiple-percentiles-in-an-array"></a>Obtenir plusieurs percentiles dans un tableau

Plusieurs percentiles peuvent être obtenus comme un tableau dans une colonne dynamique unique au lieu de plusieurs colonnes: 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/aggregations/percentiles-array-result.png" alt-text="Résultat de tableau de percentiles":::

De même, les percentiles pondérés peuvent être retournés comme un tableau dynamique à l’aide`percentilesw_array`

Percentiles `percentiles_array` pour `percentilesw_array` et peut être spécifié dans un tableau dynamique de numéros d’intégrant ou de point flottant. Le tableau doit être constant mais n’a pas besoin d’être littéral.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>Percentile le plus proche
*P*-th percentile (0 < *P* <100) d’une liste de valeurs commandées (triées du moins au plus grand) est la plus petite valeur de la liste de telle sorte que *P* pour cent des données est moins ou égale à cette valeur[(de l’article de Wikipedia sur percentiles](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method))

Définir *0*-th percentiles pour être le plus petit membre de la population.

>[!NOTE]
> Étant donné la nature approximative du calcul, la valeur réelle retournée peut ne pas être un membre de la population.
> La définition de rang le plus proche signifie que *le P*50 n’est pas conforme à la [définition interpolative de la médiane](https://en.wikipedia.org/wiki/Median). Lors de l’évaluation de l’importance de cet écart pour l’application spécifique, la taille de la population et une [erreur d’estimation](#estimation-error-in-percentiles) doivent être prises en compte.

## <a name="estimation-error-in-percentiles"></a>Erreur d’estimation dans les centiles

L’agrégation de centiles fournit une valeur approximative au moyen de [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

>[!NOTE]
> * Les limites de l’erreur d’estimation dépendent de la valeur du centile demandé. La meilleure précision est à la fin de l’échelle [0.100]. Les percentiles 0 et 100 sont les valeurs minimales et maximales exactes de la distribution. La précision diminue progressivement vers le milieu de l’échelle. Il est le pire à la médiane et est plafonné à 1%. 
> * Les limites d’erreur sont observées sur le classement, et non sur la valeur. Supposons percentile (X, 50) retourné une valeur de Xm. L’estimation garantit qu’au moins 49% et au plus 51% des valeurs de X sont moins ou égales à Xm. Il n’y a pas de limite théorique à la différence entre Xm et la valeur médiane réelle de X.
> * L’estimation peut parfois entraîner une valeur précise, mais il n’y a pas de conditions fiables pour définir quand ce sera le cas.
