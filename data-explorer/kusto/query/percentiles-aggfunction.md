---
title: centile (), centile ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit centile (), centile () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: 58d6458f0a5cf514b1acd240c9adede2022f028b
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619093"
---
# <a name="percentile-percentiles"></a>centile (), centile ()

Retourne une estimation pour le centile spécifié le [plus proche](#nearest-rank-percentile) de la population définie par *expr*. La précision dépend de la densité de population dans la région du centile. Cette fonction ne peut être utilisée que dans le contexte d’une agrégation à l’intérieur d’une [synthèse](summarizeoperator.md)

* `percentiles()`est similaire `percentile()`à, mais calcule un nombre de valeurs de centile (ce qui est plus rapide que le calcul de chaque centile individuellement).
* `percentilesw()`est similaire `percentilew()`à, mais calcule un nombre de valeurs de centile pondérées (ce qui est plus rapide que le calcul de chaque centile individuellement).
* `percentilew()`et `percentilesw()` permet de calculer les centile pondérés. Les centile pondérés calculent les centiles donnés dans un mode « pondéré », en traitant chaque valeur comme s’il s' `Weight` agissait de temps répétés dans l’entrée.

**Syntaxe**

`percentile(`résumer *expr* `,` *centile*`)`

`percentiles(`résumer *expr* `,` *Percentile1* [`,` *Percentile2*]`)`

`percentiles_array(`résumer *expr* `,` *Percentile1* [`,` *Percentile2*]`)`

`percentiles_array(`résumer le *tableau dynamique* *expr* `,``)`

`percentilew(`résumer *expr* `,` *WeightExpr* `,` *centile*`)`

`percentilesw(`résumer *expr* `,` *WeightExpr* `,` *Percentile1* [`,` *Percentile2*]`)`

`percentilesw_array(`résumer *expr* `,` *WeightExpr* `,` *Percentile1* [`,` *Percentile2*]`)`

`percentilesw_array(`résumer le *tableau dynamique* *expr* `,` *WeightExpr* `,``)`

**Arguments**

* *Expr*: expression qui sera utilisée pour le calcul de l’agrégation.
* *WeightExpr*: expression qui sera utilisée comme poids des valeurs pour le calcul de l’agrégation.
* *Centile* est une constante double qui spécifie le centile.
* *Tableau dynamique*: liste de centiles dans un tableau dynamique de nombres entiers ou à virgule flottante.

**Retourne**

Retourne une estimation pour *expr* des centiles spécifiés dans le groupe. 

**Exemples**

La valeur de `Duration` qui est supérieure à 95% de l’échantillon est définie et inférieure à 5% de l’échantillon défini :

```kusto
CallDetailRecords | summarize percentile(Duration, 95) by continent
```

Calculer simultanément 5, 50 (median) et 95 :

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95) by continent
```

:::image type="content" source="images/percentiles-aggfunction/percentiles.png" alt-text="Centiles":::

Les résultats montrent qu’en Europe, 5% des appels sont plus courts que 11.55 s, 50% des appels sont plus courts que 3 minutes, 18,46 secondes et 95% des appels sont plus courts que 40 minutes 48 secondes.

Calculer plusieurs statistiques :

```kusto
CallDetailRecords 
| summarize percentiles(Duration, 5, 50, 95), avg(Duration)
```

## <a name="weighted-percentiles"></a>Centiles pondérés

Supposons que vous mesurez le temps (durée), une action est exécutée une nouvelle fois. Au lieu d’enregistrer chaque valeur de mesure, vous pouvez les condenser en enregistrant chaque valeur de Duration, arrondi à 100 ms et le nombre de fois que la valeur arrondie est apparue (BucketSize).

Vous pouvez utiliser `summarize percentilesw(Duration, BucketSize, ...)` pour calculer les centiles donnés dans un mode « pondéré », en traitant chaque valeur de Duration comme si elle était répétée BucketSize fois dans l’entrée, sans avoir besoin de matérialiser ces enregistrements.

Exemple : un client a un ensemble de valeurs de latence en millisecondes : `{ 1, 1, 2, 2, 2, 5, 7, 7, 12, 12, 15, 15, 15, 18, 21, 22, 26, 35 }`.

Pour réduire la bande passante et le stockage, effectuez une pré-agrégation pour `{ 10, 20, 30, 40, 50, 100 }`les compartiments suivants : et comptez le nombre d’événements dans chaque compartiment, qui donne la table Kusto suivante :

:::image type="content" source="images/percentiles-aggfunction/percentilesw-table.png" alt-text="Table Percentilesw":::

Qui peut être lu comme suit :
 * 8 événements dans le compartiment 10 ms (correspondant à `{ 1, 1, 2, 2, 2, 5, 7, 7 }`un sous-ensemble)
 * 6 événements dans le compartiment 20 ms (correspondant à un `{ 12, 12, 15, 15, 15, 18 }`sous-ensemble)
 * 3 événements dans le compartiment 30ms (correspondant à un `{ 21, 22, 26 }`sous-ensemble)
 * 1 événement dans le compartiment 40ms (correspondant au sous `{ 35 }`-ensemble)

À ce stade, les données d’origine ne sont plus disponibles, et tout ce que nous avons est le nombre d’événements dans chaque compartiment. Pour calculer des centile à partir de ces données, utilisez `percentilesw()` la fonction. Par exemple, pour les centile 50, 75 et 99,9, utilisez la requête suivante : 

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


:::image type="content" source="images/percentiles-aggfunction/percentilesw-result.png" alt-text="Résultat de Percentilesw" border="false":::


Notez que la requête ci-dessus correspond à `percentiles(LatencyBucket, 50, 75, 99.9)` la fonction si les données ont été étendues au formulaire suivant :

:::image type="content" source="images/percentiles-aggfunction/percentilesw-rawtable.png" alt-text="Percentilesw table brute":::

## <a name="getting-multiple-percentiles-in-an-array"></a>Obtention de plusieurs centiles dans un tableau

Plusieurs centiles peuvent être obtenus sous la forme d’un tableau dans une seule colonne dynamique au lieu de plusieurs colonnes : 

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, 5, 25, 50, 75, 95), avg(Duration)
```

:::image type="content" source="images/percentiles-aggfunction/percentiles-array-result.png" alt-text="Résultat du tableau de centiles":::

De même, les centile pondérés peuvent être retournés sous forme de tableau dynamique à l’aide de`percentilesw_array`

Les centile pour `percentiles_array` et `percentilesw_array` peuvent être spécifiés dans un tableau dynamique de nombres entiers ou à virgule flottante. Le tableau doit être constant, mais il n’est pas nécessaire qu’il soit littéral.

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, dynamic([5, 25, 50, 75, 95])), avg(Duration)
```

```kusto
CallDetailRecords 
| summarize percentiles_array(Duration, range(0, 100, 5)), avg(Duration)
```

## <a name="nearest-rank-percentile"></a>Centile le plus proche
*P*-th centile (0 < *P* <= 100) d’une liste de valeurs ordonnées (triées du moins au plus grand) est la plus petite valeur de la liste, de telle sorte que *P* % des données sont inférieures ou égales à cette valeur ([de l’article Wikipédia sur les centile](https://en.wikipedia.org/wiki/Percentile#The_Nearest_Rank_method))

Définissez *0*-th centile pour être le plus petit membre de la population.

>[!NOTE]
> Compte tenu de la nature approximative du calcul, la valeur retournée réelle ne peut pas être un membre du remplissage.
> La définition de classement la plus proche signifie que *P*= 50 n’est pas conforme à la [définition interpolive de la médiane](https://en.wikipedia.org/wiki/Median). Lors de l’évaluation de l’importance de cette différence pour l’application spécifique, la taille du remplissage et une [erreur d’estimation](#estimation-error-in-percentiles) doivent être prises en compte.

## <a name="estimation-error-in-percentiles"></a>Erreur d’estimation dans les centiles

L’agrégation de centiles fournit une valeur approximative au moyen de [T-Digest](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

>[!NOTE]
> * Les limites de l’erreur d’estimation dépendent de la valeur du centile demandé. La meilleure précision se trouve aux extrémités de l’échelle [0.. 100]. Les centiles 0 et 100 sont les valeurs minimales et maximales exactes de la distribution. La précision diminue progressivement vers le milieu de l’échelle. C’est le pire au médian et est limité à 1%. 
> * Les limites d’erreur sont observées sur le classement, et non sur la valeur. Supposons que centile (X, 50) a retourné la valeur XM. L’estimation garantit qu’au moins 49% et au plus 51% des valeurs de X sont inférieures ou égales à XM. Il n’existe aucune limite théorique quant à la différence entre XM et la valeur médiane réelle de X.
> * L’estimation peut parfois aboutir à une valeur précise, mais il n’existe aucune condition fiable à définir quand elle sera la casse.
