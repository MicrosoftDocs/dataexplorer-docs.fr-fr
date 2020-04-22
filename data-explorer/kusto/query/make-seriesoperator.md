---
title: opérateur de la série de maquillage - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de la série make-in dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2020
ms.openlocfilehash: 3fa8f1693b56fc0820b9e0ba6b5f03a9363c9b98
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663725"
---
# <a name="make-series-operator"></a>opérateur make-series

Créez une série de valeurs agrégées spécifiées le long de l’axe spécifié. 

```kusto
T | make-series sum(amount) default=0, avg(price) default=0 on timestamp from datetime(2016-01-01) to datetime(2016-01-10) step 1d by fruit, supplier
```

**Syntaxe**

*T* `| make-series` [*MakeSeriesParamters*] [*Colonne* `=`] *Agrégation* `default` `=` [ *DefaultValue*] [`,` ...] `on` *AxisColumn* `from` *start*[ début`to` ] `step` [ *fin*] *étape* `by` [`,` [*Colonne* `=`] *GroupExpression* [...]]

**Arguments**

* *Column :* nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression.
* *DefaultValue:* Valeur par défaut qui sera utilisée au lieu de valeurs absentes. S’il n’y a pas de ligne avec des valeurs spécifiques *d’AxisColumn* et *GroupExpression,* alors dans les résultats l’élément correspondant du tableau sera attribué avec un *DefaultValue*. Si `default =` *DefaultValue* est omis, alors 0 est supposé. 
* *Agrégation:* Un appel à une [fonction d’agrégation](make-seriesoperator.md#list-of-aggregation-functions) telle que `count()` ou `avg()`, avec des noms de colonne comme arguments. Voir la [liste des fonctions d’agrégation](make-seriesoperator.md#list-of-aggregation-functions). Notez que seules les fonctions d’agrégation `make-series` qui renvoient le résultat numérique peuvent être utilisées avec l’opérateur.
* *AxisColumn:* Une colonne sur laquelle la série sera commandée. Il pourrait être considéré comme `datetime` un calendrier, mais en plus de tous les types numériques sont acceptés.
* *départ*: (facultatif) La faible valeur liée de *l’AxisColumn* pour chacune de la série sera construite. *début*, *fin* et *étape* sont utilisés pour construire un tableau de valeurs *AxisColumn* dans une plage donnée et en utilisant *l’étape*spécifiée . Toutes les valeurs *d’agrégation* sont commandées respectivement à ce tableau. Ce tableau *AxisColumn* est également la dernière colonne de sortie dans la sortie avec le même nom que *AxisColumn*. Si une valeur *de démarrage* n’est pas spécifiée, le démarrage est le premier bac (étape) qui a des données dans chaque série.
* *fin*: (facultatif) La valeur haute (non inclusive) de *l’AxisColumn*, le dernier indice de la série de temps est plus petit que cette valeur (et sera *de départ* plus integer multiple *d’étape* qui est plus petit que *la fin*). Si la valeur *finale* n’est pas fournie, elle sera la limite supérieure du dernier bac (étape) qui a des données par chaque série.
* *étape*: La différence entre deux éléments consécutifs du tableau *AxisColumn* (c’est-à-dire la taille du bac).
* *GroupExpression* : expression sur les colonnes, qui fournit un ensemble de valeurs distinctes. En règle générale, il s’agit d’un nom de colonne qui fournit déjà un ensemble limité de valeurs. 
* *MakeSeriesParameters*: Paramètres nuls ou plus (séparés dans l’espace) sous la forme de *la valeur* de *nom* `=` qui contrôlent le comportement. Les paramètres suivants sont pris en charge : 
  
  |Nom           |Valeurs                                        |Description                                                                                        |
  |---------------|-------------------------------------|------------------------------------------------------------------------------|
  |`kind`          |`nonempty`                               |Produit un résultat par défaut lorsque l’entrée de l’opérateur de la série make est vide|                                

**Retourne**

Les lignes d’entrée sont disposées en `by` groupes `bin_at(`ayant les mêmes valeurs des expressions et l’expression*de démarrage* `)` *d’étape*`, ` *AxisColumn.*`, ` Ensuite, les fonctions d’agrégation spécifiées sont calculées sur chaque groupe, générant une ligne pour chaque groupe. Le résultat `by` contient les colonnes, la colonne *AxisColumn* et également au moins une colonne pour chaque agrégat calculé. (Agrégation selon laquelle plusieurs colonnes ou résultats non numériques ne sont pas pris en charge.)

Ce résultat intermédiaire a autant de lignes qu’il existe des combinaisons distinctes de valeurs de `by` *démarrage* `)` *d’étapes*`, ` `bin_at(` *AxisColumn.*`, `

Enfin, les lignes du résultat intermédiaire disposées en `by` groupes ayant les mêmes valeurs des expressions `dynamic` et toutes les valeurs agrégées sont disposées en tableaux (valeurs de type). Pour chaque agrégation il y a une colonne contenant son tableau du même nom. La dernière colonne dans la sortie de la fonction de gamme avec toutes les valeurs *AxisColumn.* Sa valeur est répétée pour toutes les rangées. 

Notez qu’en raison du remplissage des bacs manquants par valeur par défaut, le tableau pivot résultant a le même nombre de bacs (c.-à-d. les valeurs agrégées) pour toutes les séries  

**Remarque**

Bien que vous puissiez fournir des expressions arbitraires pour les expressions d’agrégation et de regroupement, il est plus efficace d’utiliser des noms de colonnes simples.

**Syntaxe alternative**

*T* `| make-series` [*Colonne* `=`]`default` `=` *Agrégation* [`,` *DefaultValue*] [ ...] `on` *AxisColumn* `in` `range(` *start* `,` *stop* `,` *step* `)` [`by` [*Colonne* `=`] *GroupExpression* [`,` ...]]

La série générée à partir de la syntaxe alternative diffère de la syntaxe principale en 2 aspects:
* La valeur *d’arrêt* est inclusive.
* Binning l’axe de l’index est généré avec bin () et non bin_at () ce qui signifie que *le démarrage* n’est pas garanti d’être inclus dans la série générée.

Il est recommandé d’utiliser la syntaxe principale de la série make et non la syntaxe alternative.

**Distribution et shuffle**

`make-series`prend `summarize` en charge [les conseils shufflekey](shufflequery.md) à l’aide de la syntaxe hint.shufflekey.

## <a name="list-of-aggregation-functions"></a>Liste des fonctions d’agrégation

|Fonction|Description|
|--------|-----------|
|[n’importe(importe))](any-aggfunction.md)|Retourne la valeur aléatoire non vide pour le groupe|
|[avg()](avg-aggfunction.md)|Retuns la valeur moyenne dans l’ensemble du groupe|
|[compter()](count-aggfunction.md)|Nombre de retours du groupe|
|[countif()](countif-aggfunction.md)|Les retours comptent avec le prédicat du groupe|
|[dcount()](dcount-aggfunction.md)|Rendements nombre distincts d’éléments du groupe|
|[max()](max-aggfunction.md)|Retourne la valeur maximale dans l’ensemble du groupe|
|[min()](min-aggfunction.md)|Retourne la valeur minimale dans l’ensemble du groupe|
|[stdev()](stdev-aggfunction.md)|Retourne l’écart standard dans l’ensemble du groupe|
|[sum()](sum-aggfunction.md)|Retourne la somme des éléments avec le groupe|
|[variance()](variance-aggfunction.md)|Retourne l’écart à travers le groupe|

## <a name="list-of-series-analysis-functions"></a>Liste des fonctions d’analyse de série

|Fonction|Description|
|--------|-----------|
|[series_fir()](series-firfunction.md)|Applique le filtre [Finie Impulse Response](https://en.wikipedia.org/wiki/Finite_impulse_response)|
|[series_iir()](series-iirfunction.md)|Applique le filtre [Infinite Impulse Response](https://en.wikipedia.org/wiki/Infinite_impulse_response)|
|[series_fit_line()](series-fit-linefunction.md)|Trouve une ligne droite qui est la meilleure approximation de l’entrée|
|[series_fit_line_dynamic()](series-fit-line-dynamicfunction.md)|Trouve une ligne qui est la meilleure approximation de l’entrée, retour objet dynamique|
|[series_fit_2lines()](series-fit-2linesfunction.md)|Trouve deux lignes qui est la meilleure approximation de l’entrée|
|[series_fit_2lines_dynamic()](series-fit-2lines-dynamicfunction.md)|Trouve deux lignes qui est la meilleure approximation de l’entrée, retour objet dynamique|
|[series_outliers()](series-outliersfunction.md)|Scores anomaly points in a series|
|[series_periods_detect()](series-periods-detectfunction.md)|Trouve les périodes les plus importantes qui existent dans une série de temps|
|[series_periods_validate()](series-periods-validatefunction.md)|Vérifie si une série de temps contient des modèles périodiques de longueurs données|
|[series_stats_dynamic()](series-stats-dynamicfunction.md)|Retourner plusieurs colonnes avec les statistiques communes (min/max/variance/stdev/average)|
|[series_stats()](series-statsfunction.md)|Génère une valeur dynamique avec les statistiques communes (min/max/variance/stdev/average)|
  
## <a name="list-of-series-interpolation-functions"></a>Liste des fonctions d’interpolation en série
|Fonction|Description|
|--------|-----------|
|[series_fill_backward()](series-fill-backwardfunction.md)|Effectue l’interpolation de remplissage vers l’arrière des valeurs manquantes dans une série|
|[series_fill_const()](series-fill-constfunction.md)|Remplace les valeurs manquantes d’une série par une valeur constante spécifiée|
|[series_fill_forward()](series-fill-forwardfunction.md)|Effectue l’interpolation de remplissage vers l’avant des valeurs manquantes dans une série|
|[series_fill_linear()](series-fill-linearfunction.md)|Effectue l’interpolation linéaire des valeurs manquantes dans une série|

* Remarque : Les fonctions `null` d’interpolation par défaut supposent qu’il s’tion d’une valeur manquante. Par conséquent, il `default=`est`null`recommandé `make-series` de spécifier *double*( ) si vous avez l’intention d’utiliser des fonctions d’interpolation pour la série. 

## <a name="example"></a>Exemple
  
 Un tableau qui montre des tableaux des nombres et des prix moyens de chaque fruit de chaque fournisseur commandé par l’ampoule avec une gamme spécifiée. Il ya une rangée dans la sortie pour chaque combinaison distincte de fruits et de fournisseur. Les colonnes de sortie montrent le fruit, le fournisseur et les tableaux de: compter, la moyenne et l’ensemble de la ligne de temps (de 2016-01-01 jusqu’en 2016-01-10). Tous les tableaux sont triés par l’échéampe respectif et toutes les lacunes sont comblées par des valeurs par défaut (0 dans cet exemple). Toutes les autres colonnes d’entrée sont supprimées.
  
```kusto
T | make-series PriceAvg=avg(Price) default=0
on Purchase from datetime(2016-09-10) to datetime(2016-09-13) step 1d by Supplier, Fruit
```

:::image type="content" source="images/aggregations/makeseries.png" alt-text="Série de makeseries":::  
  
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
|[ 4.0, 3.0, 5.0, 0.0, 10.5, 4.0, 3.0, 8.0, 6.5 ]|[ "2017-01-01T00:00:00.0000000Z", "2017-01-02T00:00.0000000Z", "2017-01-03T00:00:00.00000000Z", "2017-01-04T00:00:00.000000Z", "2017-01-05T00:00.000000Z", "2017-01-05T00:00:00.000000Z", "2017-01-05T00:0000:00.0000000Z", "2017-01-06T00:00:00.000000Z", "2017-01-07T00:00:00.00000000Z", "2017-01-08T00:00:00.0000000Z", "2017-01-09T00:00:00.000000Z" ]|  


Lorsque l’entrée `make-series` est vide, `make-series` le comportement par défaut de produit un résultat vide ainsi.

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

|Count|
|---|
|0|


L’utilisation `kind=nonempty` produira `make-series` un résultat non vide des valeurs par défaut :

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