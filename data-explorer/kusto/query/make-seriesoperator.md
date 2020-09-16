---
title: opérateur make-Series-Azure Explorateur de données
description: Cet article décrit l’opérateur make-Series dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 6ed841a6f47eb9a0a1e73182a3b9acd1c0209bd9
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90680757"
---
# <a name="make-series-operator"></a>opérateur make-series

Crée une série de valeurs agrégées spécifiées le long d’un axe spécifié.

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>Syntaxe

*T* `| make-series` [*MakeSeriesParamters*] [*Column* `=` ] *Aggregation* [ `default` `=` *DefaultValue*] [ `,` ...] `on` *AxisColumn* [ `from` *Start*] [ `to` *end*] `step` *étape* [ `by` [*Column* `=` ] *GroupExpression* [ `,` ...]]

## <a name="arguments"></a>Arguments

* *Column :* nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression.
* *DefaultValue :* Valeur par défaut qui sera utilisée à la place des valeurs absentes. S’il n’y a pas de ligne avec des valeurs spécifiques de *AxisColumn* et *GroupExpression*, alors dans, les résultats de l’élément correspondant du tableau sont affectés à une valeur *DefaultValue*. Si *DefaultValue* est omis, 0 est utilisé par défaut. 
* *Agrégation :* Appel à une [fonction d’agrégation](make-seriesoperator.md#list-of-aggregation-functions) telle que `count()` ou `avg()` , avec les noms de colonnes comme arguments. Voir la [liste des fonctions d’agrégation](make-seriesoperator.md#list-of-aggregation-functions). Seules les fonctions d’agrégation qui retournent des résultats numériques peuvent être utilisées avec l' `make-series` opérateur.
* *AxisColumn :* Colonne sur laquelle la série sera classée. Elle peut être considérée comme une chronologie, mais en plus `datetime` des types numériques acceptés.
* *Start*: (facultatif) la valeur de la limite inférieure de *AxisColumn* pour chacune des séries à générer. les instructions *Start*, *end*et *Step* sont utilisées pour générer un tableau de valeurs *AxisColumn* dans une plage donnée et à l’aide de l' *étape*spécifiée. Toutes les valeurs d' *agrégation* sont classées respectivement dans ce tableau. Ce tableau *AxisColumn* est également la dernière colonne de sortie de la sortie qui porte le même nom que *AxisColumn*. Si aucune valeur de *départ* n’est spécifiée, le début est le premier emplacement (étape) qui contient des données dans chaque série.
* *end*: (facultatif) valeur de la limite supérieure (non inclusive) de *AxisColumn*. Le dernier index de la série chronologique est plus petit que cette valeur (et sera de *début* plus entier multiple de l' *étape* qui est plus petit que la *fin*). Si la valeur de *fin* n’est pas fournie, il s’agit de la limite supérieure du dernier compartiment (étape) qui contient des données par série.
* *Step*: différence entre deux éléments consécutifs du tableau *AxisColumn* (autrement dit, la taille de l’emplacement).
* *GroupExpression :* Expression sur les colonnes qui fournit un ensemble de valeurs distinctes. En règle générale, il s’agit d’un nom de colonne qui fournit déjà un ensemble limité de valeurs. 
* *MakeSeriesParameters*: zéro, un ou plusieurs paramètres (séparés par des espaces) sous la forme d’une valeur de *nom* `=` *Value* qui contrôlent le comportement. Les paramètres suivants sont pris en charge : 
  
  |Nom           |Valeurs                                        |Description                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |Produit un résultat par défaut lorsque l’entrée de l’opérateur de série make est vide|                                

## <a name="returns"></a>Retours

Les lignes d’entrée sont organisées en groupes ayant les mêmes valeurs d' `by` expressions et l' `bin_at(` *AxisColumn* `, ` expression de début d'*étape*AxisColumn `, ` *start* `)` . Ensuite, les fonctions d’agrégation spécifiées sont calculées sur chaque groupe, générant une ligne pour chaque groupe. Le résultat contient les `by` colonnes, la colonne *AxisColumn* et également au moins une colonne pour chaque agrégat calculé. (L’agrégation qui ne prend pas en charge les colonnes multiples ou les résultats non numériques).

Ce résultat intermédiaire contient autant de lignes qu’il y a de combinaisons distinctes de `by` valeurs de `bin_at(` *AxisColumn* `, ` début d'*étape*AxisColumn et `, ` *start* `)` .

Enfin, les lignes du résultat intermédiaire organisées en groupes ayant les mêmes valeurs des `by` expressions et toutes les valeurs agrégées sont organisées en tableaux (valeurs de `dynamic` type). Pour chaque agrégation, il existe une colonne contenant son tableau portant le même nom. Dernière colonne dans la sortie de la fonction Range avec toutes les valeurs *AxisColumn* . Sa valeur est répétée pour toutes les lignes. 

En raison de la valeur par défaut des emplacements de remplissage manquants, le tableau croisé dynamique résultant a le même nombre d’emplacements (c’est-à-dire, de valeurs agrégées) pour toutes les séries  

**Remarque**

Bien que vous puissiez fournir des expressions arbitraires pour les expressions d’agrégation et de regroupement, il est plus efficace d’utiliser des noms de colonne simples.

**Autre syntaxe**

*T* `| make-series` [*Column* `=` ] *Aggregation* [ `default` `=` *DefaultValue*] [ `,` ...] `on` *AxisColumn* `in` `range(` *Démarrer* l’étape d' `,` *arrêt* `,` *step* `)` [ `by` [*colonne* `=` ] *GroupExpression* [ `,` ...]]

Les séries générées de la syntaxe alternative diffèrent de la syntaxe principale en deux aspects :
* La valeur d' *arrêt* est inclusive.
* Compartimentage l’axe d’index est généré avec bin () et non bin_at (), ce qui signifie que *Start* ne peut pas être inclus dans la série générée.

Il est recommandé d’utiliser la syntaxe principale de make-Series et non la syntaxe alternative.

**Distribution et lecture aléatoire**

`make-series` prend en charge `summarize` les [indicateurs shufflekey](shufflequery.md) à l’aide de l’indicateur de syntaxe. shufflekey.

## <a name="list-of-aggregation-functions"></a>Liste des fonctions d’agrégation

|Fonction|Description|
|--------|-----------|
|[Any ()](any-aggfunction.md)|Retourne une valeur non vide aléatoire pour le groupe|
|[avg()](avg-aggfunction.md)|Retourne une valeur moyenne dans le groupe|
|[avgif()](avgif-aggfunction.md)|Retourne une moyenne avec le prédicat du groupe.|
|[Count ()](count-aggfunction.md)|Retourne le nombre de groupes|
|[countif()](countif-aggfunction.md)|Retourne un nombre avec le prédicat du groupe.|
|[dcount()](dcount-aggfunction.md)|Retourne un nombre approximatif distinct des éléments de groupe|
|[dcountif()](dcountif-aggfunction.md)|Retourne un compte approximatif distinct avec le prédicat du groupe|
|[max()](max-aggfunction.md)|Retourne la valeur maximale dans l'ensemble du groupe|
|[maxif()](maxif-aggfunction.md)|Retourne la valeur maximale avec le prédicat du groupe.|
|[min()](min-aggfunction.md)|Retourne la valeur minimale dans l'ensemble du groupe|
|[minif()](minif-aggfunction.md)|Retourne la valeur minimale avec le prédicat du groupe.|
|[stdev()](stdev-aggfunction.md)|Retourne l’écart type de l’ensemble du groupe|
|[sum()](sum-aggfunction.md)|Retourne la somme des éléments dans le groupe|
|[sumif()](sumif-aggfunction.md)|Retourne la somme des éléments avec le prédicat du groupe.|
|[variance()](variance-aggfunction.md)|Retourne la variance dans le groupe|

## <a name="list-of-series-analysis-functions"></a>Liste des fonctions d’analyse de série

|Fonction|Description|
|--------|-----------|
|[series_fir()](series-firfunction.md)|Applique un filtre à [réponse impulsionnelle finie](https://en.wikipedia.org/wiki/Finite_impulse_response)|
|[series_iir()](series-iirfunction.md)|Applique un filtre à [réponse impulsionnelle infinie](https://en.wikipedia.org/wiki/Infinite_impulse_response)|
|[series_fit_line()](series-fit-linefunction.md)|Recherche une ligne droite qui est la meilleure approximation de l’entrée|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Recherche une ligne qui est la meilleure approximation de l’entrée, en retournant l’objet dynamique|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Recherche deux lignes qui représentent la meilleure approximation de l’entrée|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Recherche deux lignes qui représentent la meilleure approximation de l’entrée, en retournant l’objet dynamique|
|[series_outliers()](series-outliersfunction.md)|Notation des points d’anomalies dans une série|
|[series_periods_detect()](series-periods-detectfunction.md)|Recherche les périodes les plus significatives qui existent dans une série chronologique|
|[series_periods_validate()](series-periods-validatefunction.md)|Vérifie si une série chronologique contient des modèles périodiques de longueurs données|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Retourner plusieurs colonnes avec les statistiques courantes (min/max/variance/ECARTYPE/moyenne)|
|[series_stats()](series-statsfunction.md)|Génère une valeur dynamique avec les statistiques courantes (min/max/variance/ECARTYPE/moyenne)|
  
## <a name="list-of-series-interpolation-functions"></a>Liste des fonctions d’interpolation de série

|Fonction|Description|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|Effectue l’interpolation de remplissage arrière des valeurs manquantes dans une série|
|[series_fill_const()](series-fill-constfunction.md)|Remplace les valeurs manquantes dans une série par une valeur de constante spécifiée|
|[series_fill_forward()](series-fill-forwardfunction.md)|Effectue l’interpolation de remplissage par progression des valeurs manquantes dans une série|
|[series_fill_linear()](series-fill-linearfunction.md)|Effectue une interpolation linéaire des valeurs manquantes dans une série|

* Remarque : les fonctions d’interpolation par défaut prennent `null` la valeur manquante. Par conséquent, spécifiez `default=` *double*( `null` ) dans `make-series` si vous envisagez d’utiliser des fonctions d’interpolation pour la série. 

## <a name="example"></a>Exemple
  
 Table qui affiche des tableaux des nombres et les prix moyens de chaque fruit de chaque fournisseur classé par horodatage avec la plage spécifiée. La sortie contient une ligne pour chaque combinaison de fruits et de fournisseurs. Les colonnes de sortie affichent les fruits, les fournisseurs et les tableaux de : Count, Average et la chronologie entière (de 2016-01-01 jusqu’à 2016-01-10). Tous les tableaux sont triés selon l’horodatage respectif et tous les espaces sont remplis avec des valeurs par défaut (0 dans cet exemple). Toutes les autres colonnes d’entrée sont supprimées.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="Makeseries":::  

<!-- csl: https://help.kusto.windows.net:443/Samples --> 
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| make-series avg(metric) on timestamp from stime to etime step interval 
```
  
|avg_metric|timestamp|
|---|---|
|[4,0, 3,0, 5,0, 0,0, 10,5, 4,0, 3,0, 8,0, 6,5]|["2017-01-01T00:00:00.0000000 Z", "2017-01-02T00:00:00.0000000 Z", "2017-01-03T00:00:00.0000000 Z", "2017-01-04T00:00:00.0000000 Z", "2017-01-05T00:00:00.0000000 Z", "2017-01-06T00:00:00.0000000 Z", "2017-01-07T00:00:00.0000000 Z", "2017-01-08T00:00:00.0000000 Z", "2017-01-09T00:00:00.0000000 Z"]|  


Lorsque l’entrée de `make-series` est vide, le comportement par défaut de `make-series` produit également un résultat vide.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series avg(metric) default=1.0 on timestamp from stime to etime step interval 
| count 
```

|Nombre|
|---|
|0|


L’utilisation de dans produira `kind=nonempty` `make-series` un résultat non vide des valeurs par défaut :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let data=datatable(timestamp:datetime, metric: real)
[
  datetime(2016-12-31T06:00), 50,
  datetime(2017-01-01), 4,
  datetime(2017-01-02), 3,
  datetime(2017-01-03), 4,
  datetime(2017-01-03T03:00), 6,
  datetime(2017-01-05), 8,
  datetime(2017-01-05T13:40), 13,
  datetime(2017-01-06), 4,
  datetime(2017-01-07), 3,
  datetime(2017-01-08), 8,
  datetime(2017-01-08T21:00), 8,
  datetime(2017-01-09), 2,
  datetime(2017-01-09T12:00), 11,
  datetime(2017-01-10T05:00), 5,
];
let interval = 1d;
let stime = datetime(2017-01-01);
let etime = datetime(2017-01-10);
data
| limit 0
| make-series kind=nonempty avg(metric) default=1.0 on timestamp from stime to etime step interval 
```

|avg_metric|timestamp|
|---|---|
|[<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1,0,<br>  1.0<br>]|[<br>  « 2017-01-01T00:00:00.0000000 Z »,<br>  « 2017-01-02T00:00:00.0000000 Z »,<br>  « 2017-01-03T00:00:00.0000000 Z »,<br>  « 2017-01-04T00:00:00.0000000 Z »,<br>  « 2017-01-05T00:00:00.0000000 Z »,<br>  « 2017-01-06T00:00:00.0000000 Z »,<br>  « 2017-01-07T00:00:00.0000000 Z »,<br>  « 2017-01-08T00:00:00.0000000 Z »,<br>  « 2017-01-09T00:00:00.0000000 Z »<br>]|
