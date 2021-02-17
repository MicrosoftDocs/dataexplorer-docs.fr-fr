---
title: Opérateur make-series - Azure Data Explorer
description: Cet article décrit l'opérateur make-series dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.localizationpriority: high
ms.openlocfilehash: 29fdacc9483c21c4a8a148d2134f4082d439bb89
ms.sourcegitcommit: ee49cd8186d4aecd5de1ed6d24db6c7b7a079ac4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549001"
---
# <a name="make-series-operator"></a>opérateur make-series

Créez une série de valeurs agrégées spécifiées le long d'un axe donné.

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

## <a name="syntax"></a>Syntaxe

*T* `| make-series` [*MakeSeriesParamters*] [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* [`from` *start*] [`to` *end*] `step` *step* [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>Arguments

* *Column :* nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression.
* *DefaultValue :* valeur par défaut utilisée à la place des valeurs manquantes. En l'absence de ligne comportant des valeurs spécifiques pour *AxisColumn* et *GroupExpression*, une valeur par défaut (*DefaultValue*) est attribuée à l'élément correspondant du tableau dans les résultats. En l'absence de valeur *DefaultValue*, la valeur 0 est utilisée par défaut. 
* *Agrégation :* appel d'une [fonction d'agrégation](make-seriesoperator.md#list-of-aggregation-functions) telle que `count()` ou `avg()`, avec des noms de colonne comme arguments. Voir la [liste des fonctions d’agrégation](make-seriesoperator.md#list-of-aggregation-functions). Seules les fonctions d'agrégation qui renvoient des résultats numériques peuvent être utilisées avec l'opérateur `make-series`.
* *AxisColumn :* colonne à partir de laquelle la série est classée. Elle peut être considérée comme une chronologie, mais hormis `datetime` tous les types numériques sont acceptés.
* *start* (facultatif) : valeur limite inférieure d'*AxisColumn* pour chacune des séries à générer. *start*, *end* et *step* sont utilisés pour créer un tableau de valeurs *AxisColumn* dans une plage donnée et en utilisant l'argument *step* spécifié. Toutes les valeurs *Aggregation* sont respectivement classées dans ce tableau. Ce tableau *AxisColumn* est également la dernière colonne de la sortie qui porte le même nom qu'*AxisColumn*. Si aucune valeur *start* n'est spécifiée, il s'agit de la première classe (step) qui contient des données dans chaque série.
* *end*(facultatif) : valeur limite supérieure (non inclusive) d'*AxisColumn*. Le dernier index de la série chronologique est inférieur à cette valeur (et correspond à *start* plus un multiple entier de *step* inférieur à *end*). Si aucune valeur *end* n'est fournie, il s'agit de la limite supérieure de la dernière classe (step) qui contient des données pour chaque série.
* *step* : différence entre deux éléments consécutifs du tableau *AxisColumn* (à savoir, la taille de la classe).
* *GroupExpression :* expression sur les colonnes qui fournit un ensemble de valeurs distinctes. En règle générale, il s’agit d’un nom de colonne qui fournit déjà un ensemble limité de valeurs. 
* *MakeSeriesParameters* : zéro ou plusieurs paramètres (séparés par des espaces), sous la forme *Nom* `=` *Valeur*, qui contrôlent le comportement. Les paramètres suivants sont pris en charge : 
  
  |Nom           |Valeurs                                        |Description                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |Produit un résultat par défaut lorsque l'entrée de l'opérateur make-series est vide|                                

## <a name="returns"></a>Retours

Les lignes d'entrée sont organisées en groupes qui ont les mêmes valeurs que les expressions `by` et que l'expression `bin_at(`*AxisColumn*`, `*step*`, `*start*`)`. Ensuite, les fonctions d’agrégation spécifiées sont calculées sur chaque groupe, générant une ligne pour chaque groupe. Le résultat contient les colonnes `by`, la colonne *AxisColumn* et au moins une colonne pour chaque agrégation calculée. (Agrégation qui ne prend pas en charge les colonnes multiples ou les résultats non numériques).

Ce résultat intermédiaire comporte autant de lignes qu'il y a de combinaisons distinctes de valeurs `by` et `bin_at(`*AxisColumn*`, `*step*`, `*start*`)`.

Enfin, les lignes du résultat intermédiaire sont organisées en groupes qui ont les mêmes valeurs que les expressions `by`, et toutes les valeurs agrégées sont organisées en tableaux (valeurs de type `dynamic`). Pour chaque agrégation, il existe une colonne contenant le tableau correspondant du même nom. La dernière colonne de la sortie de la fonction range contient toutes les valeurs d'*AxisColumn*. Sa valeur est reportée sur toutes les lignes. 

En raison de la valeur par défaut des classes de remplissage manquantes, le tableau croisé dynamique qui en résulte comporte le même nombre de classes (c'est-à-dire, de valeurs agrégées) pour toutes les séries.  

**Remarque**

Bien que vous puissiez fournir des expressions arbitraires pour les expressions d'agrégation et de regroupement, il est plus efficace d'utiliser des noms de colonne simples.

**Syntaxe alternative**

*T* `| make-series` [*Column* `=`] *Aggregation* [`default` `=` *DefaultValue*] [`,` ...] `on` *AxisColumn* `in` `range(`*start*`,` *stop*`,` *step*`)` [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

Une série générée à partir de la syntaxe alternative diffère d'une série générée à partir de la syntaxe principale par deux aspects :
* La valeur *stop* est inclusive.
* Le binning de l'axe d'indexation est généré avec bin() et non bin_at(), ce qui signifie que *start* peut ne pas être inclus dans la série générée.

Il est recommandé d'utiliser la syntaxe principale de make-series plutôt que la syntaxe alternative.

**Distribution et lecture aléatoire**

`make-series` prend en charge les [indicateurs shufflekey](shufflequery.md) `summarize` avec la syntaxe hint.shufflekey.

## <a name="list-of-aggregation-functions"></a>Liste des fonctions d'agrégation

|Fonction|Description|
|--------|-----------|
|[any()](any-aggfunction.md)|Renvoie une valeur non vide aléatoire pour le groupe|
|[avg()](avg-aggfunction.md)|Renvoie une valeur moyenne pour l'ensemble du groupe|
|[avgif()](avgif-aggfunction.md)|Renvoie une moyenne avec le prédicat du groupe|
|[count()](count-aggfunction.md)|Renvoie un nombre du groupe|
|[countif()](countif-aggfunction.md)|Renvoie un nombre avec le prédicat du groupe|
|[dcount()](dcount-aggfunction.md)|Renvoie un nombre approximatif distinct des éléments de groupe|
|[dcountif()](dcountif-aggfunction.md)|Renvoie un compte distinct approximatif avec le prédicat du groupe|
|[max()](max-aggfunction.md)|Retourne la valeur maximale dans l'ensemble du groupe|
|[maxif()](maxif-aggfunction.md)|Renvoie la valeur maximale avec le prédicat du groupe|
|[min()](min-aggfunction.md)|Retourne la valeur minimale dans l'ensemble du groupe|
|[minif()](minif-aggfunction.md)|Renvoie la valeur minimale avec le prédicat du groupe|
|[percentile()](percentiles-aggfunction.md)|Retourne la valeur de percentile dans l’ensemble du groupe|
|[stdev()](stdev-aggfunction.md)|Renvoie l'écart type dans l'ensemble du groupe|
|[sum()](sum-aggfunction.md)|Renvoie la somme des éléments du groupe|
|[sumif()](sumif-aggfunction.md)|Renvoie la somme des éléments avec le prédicat du groupe|
|[variance()](variance-aggfunction.md)|Renvoie la variance dans l'ensemble du groupe|

## <a name="list-of-series-analysis-functions"></a>Liste des fonctions d'analyse de séries

|Fonction|Description|
|--------|-----------|
|[series_fir()](series-firfunction.md)|Applique un filtre à [réponse impulsionnelle finie](https://en.wikipedia.org/wiki/Finite_impulse_response)|
|[series_iir()](series-iirfunction.md)|Applique un filtre à [réponse impulsionnelle infinie](https://en.wikipedia.org/wiki/Infinite_impulse_response)|
|[series_fit_line()](series-fit-linefunction.md)|Recherche une ligne droite qui représente la meilleure approximation de l'entrée|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Recherche une ligne qui représente la meilleure approximation de l'entrée, en renvoyant l'objet dynamique|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Recherche deux lignes qui représentent la meilleure approximation de l'entrée|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Recherche deux lignes qui représentent la meilleure approximation de l'entrée, en renvoyant l'objet dynamique|
|[series_outliers()](series-outliersfunction.md)|Note les points d'anomalie dans une série|
|[series_periods_detect()](series-periods-detectfunction.md)|Recherche les périodes les plus significatives qui existent dans une série chronologique|
|[series_periods_validate()](series-periods-validatefunction.md)|Vérifie si une série chronologique contient des modèles périodiques de longueurs données|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Renvoie plusieurs colonnes comportant les statistiques courantes (min/max/variance/stdev/average)|
|[series_stats()](series-statsfunction.md)|Génère une valeur dynamique avec les statistiques courantes (min/max/variance/stdev/average)|

Pour une liste complète des fonctions d’analyse de séries, consultez : [Fonctions de traitement des séries](scalarfunctions.md#series-processing-functions)

## <a name="list-of-series-interpolation-functions"></a>Liste des fonctions d'interpolation des séries

|Fonction|Description|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|Effectue une interpolation de remplissage vers l'arrière des valeurs manquantes d'une série|
|[series_fill_const()](series-fill-constfunction.md)|Remplace les valeurs manquantes d'une série par une valeur constante spécifiée|
|[series_fill_forward()](series-fill-forwardfunction.md)|Effectue une interpolation de remplissage vers l'avant des valeurs manquantes d'une série|
|[series_fill_linear()](series-fill-linearfunction.md)|Effectue une interpolation linéaire des valeurs manquantes d'une série|

* Remarque : par défaut, les fonctions d'interpolation considèrent `null` comme une valeur manquante. Par conséquent, spécifiez `default=`*double*(`null`) dans `make-series` si vous envisagez d'utiliser des fonctions d'interpolation pour la série. 

## <a name="example"></a>Exemple
  
 Table présentant des nombres et des prix moyens par fruit et par fournisseur, classés en fonction du timestamp avec la plage spécifiée. La sortie contient une ligne pour chaque combinaison de fruits et de fournisseurs. Les colonnes de la sortie présentent les fruits, les fournisseurs et les tableaux : nombre, moyenne et ensemble de la chronologie (du 2016-01-01 au 2016-01-10). Tous les tableaux sont classés en fonction du timestamp, et tous les espaces vides sont remplis avec les valeurs par défaut (0 dans cet exemple). Toutes les autres colonnes d’entrée sont supprimées.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/make-seriesoperator/makeseries.png" alt-text="Trois tables. La première répertorie les données brutes, la deuxième ne contient que des combinaisons distinctes de fournisseurs, de fruits et de dates, et le troisième contient les résultats de la fonction make-series.":::  

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
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[ "2017-01-01T00:00:00.0000000Z", "2017-01-02T00:00:00.0000000Z", "2017-01-03T00:00:00.0000000Z", "2017-01-04T00:00:00.0000000Z", "2017-01-05T00:00:00.0000000Z", "2017-01-06T00:00:00.0000000Z", "2017-01-07T00:00:00.0000000Z", "2017-01-08T00:00:00.0000000Z", "2017-01-09T00:00:00.0000000Z" ]|  


Lorsque l'entrée de `make-series` est vide, le comportement par défaut de `make-series` produit également un résultat vide.

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


L'utilisation de `kind=nonempty` dans `make-series` produit un résultat non vide pour les valeurs par défaut :

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
|[<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0,<br>  1.0<br>]|[<br>  "2017-01-01T00:00:00.0000000Z",<br>  "2017-01-02T00:00:00.0000000Z",<br>  "2017-01-03T00:00:00.0000000Z",<br>  "2017-01-04T00:00:00.0000000Z",<br>  "2017-01-05T00:00:00.0000000Z",<br>  "2017-01-06T00:00:00.0000000Z",<br>  "2017-01-07T00:00:00.0000000Z",<br>  "2017-01-08T00:00:00.0000000Z",<br>  "2017-01-09T00:00:00.0000000Z"<br>]|
