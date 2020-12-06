---
title: Opérateur summarize - Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’opérateur summarize dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.localizationpriority: high
ms.openlocfilehash: 810eba264c717d156f74b9958edecb712d58a4fd
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513197"
---
# <a name="summarize-operator"></a>opérateur summarize

Génère une table qui agrège le contenu de la table d’entrée.

```kusto
Sales | summarize NumTransactions=count(), Total=sum(UnitPrice * NumUnits) by Fruit, StartOfMonth=startofmonth(SellDateTime)
```

Retourne une table avec le nombre de transactions de vente et le montant total par fruit et mois de vente.
Les colonnes de sortie indiquent le nombre de transactions, la valeur de transaction, le fruit et la valeur DateTime correspondant au début du mois au cours duquel la transaction a été enregistrée.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

Une table indiquant le nombre d’éléments ayant un prix dans chaque intervalle [0,10.0], [10.0,20.0] et ainsi de suite. Cet exemple comprend une colonne pour le nombre et une autre pour la gamme de prix. Toutes les autres colonnes d’entrée sont supprimées.

## <a name="syntax"></a>Syntaxe

*T* `| summarize` [[*Column* `=`] *Aggregation* [`,` ...]] [`by` [*Column* `=`] *GroupExpression* [`,` ...]]

## <a name="arguments"></a>Arguments

* *Column :* nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression.
* *Agrégation :* appel d’une [fonction d’agrégation](summarizeoperator.md#list-of-aggregation-functions) telle que `count()` ou `avg()`, avec des noms de colonne comme arguments. Voir la [liste des fonctions d’agrégation](summarizeoperator.md#list-of-aggregation-functions).
* *GroupExpression :* expression scalaire qui peut faire référence aux données d’entrée.
  La sortie a autant d’enregistrements qu’il y a de valeurs distinctes pour toutes les expressions de groupe.

> [!NOTE]
> Lorsque la table d’entrée est vide, la sortie varie selon que *GroupExpression* est utilisé :
>
> * Si *GroupExpression* n’est pas fourni, la sortie est une ligne unique (vide).
> * Si *GroupExpression* est fourni, la sortie n’a pas de ligne.

## <a name="returns"></a>Retours

Les lignes d’entrée sont organisées en groupes ayant les mêmes valeurs que les expressions `by` . Ensuite, les fonctions d’agrégation spécifiées sont calculées sur chaque groupe, générant une ligne pour chaque groupe. Le résultat contient les colonnes `by` et au moins une colonne pour chaque agrégation calculée. (Certaines fonctions d’agrégation retournent plusieurs colonnes.)

Le résultat contient autant de lignes qu’il existe de combinaisons de valeurs `by` (et peut être égal à zéro). Si aucune clé de groupe n’est fournie, le résultat comporte un seul enregistrement.

Pour générer une synthèse sur des plages de valeurs numériques, utilisez `bin()` pour limiter les plages aux valeurs discrètes.

> [!NOTE]
> * Bien que vous puissiez fournir des expressions arbitraires pour les expressions d’agrégation et de regroupement, il est plus efficace d’utiliser des noms de colonne simples ou d’appliquer `bin()` à une colonne numérique.
> * Les emplacements horaires automatiques pour les colonnes DateTime ne sont plus pris en charge. Utilisez à la place le binning explicite. Par exemple : `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>Liste des fonctions d’agrégation

|Fonction|Description|
|--------|-----------|
|[any()](any-aggfunction.md)|Retourne une valeur non vide aléatoire pour le groupe|
|[anyif()](anyif-aggfunction.md)|Retourne une valeur non vide aléatoire pour le groupe (avec un prédicat)|
|[arg_max()](arg-max-aggfunction.md)|Retourne une ou plusieurs expressions lorsque l’argument est agrandi|
|[arg_min()](arg-min-aggfunction.md)|Retourne une ou plusieurs expressions lorsque l’argument est réduit|
|[avg()](avg-aggfunction.md)|Retourne une valeur moyenne dans le groupe|
|[avgif()](avgif-aggfunction.md)|Retourne une valeur moyenne dans le groupe (avec le prédicat)|
|[binary_all_and](binary-all-and-aggfunction.md)|Retourne une valeur agrégée à l’aide du binaire `AND` du groupe|
|[binary_all_or](binary-all-or-aggfunction.md)|Retourne une valeur agrégée à l’aide du binaire `OR` du groupe|
|[binary_all_xor](binary-all-xor-aggfunction.md)|Retourne une valeur agrégée à l’aide du binaire `XOR` du groupe|
|[buildschema()](buildschema-aggfunction.md)|Retourne le schéma minimal qui admet toutes les valeurs de l’entrée `dynamic`|
|[count()](count-aggfunction.md)|Retourne un nombre du groupe|
|[countif()](countif-aggfunction.md)|Retourne un nombre avec le prédicat du groupe|
|[dcount()](dcount-aggfunction.md)|Retourne un nombre approximatif distinct des éléments de groupe|
|[dcountif()](dcountif-aggfunction.md)|Retourne un nombre approximatif distinct des éléments de groupe (avec un prédicat)|
|[make_bag()](make-bag-aggfunction.md)|Retourne un jeu de propriétés de valeurs dynamiques dans le groupe|
|[make_bag_if()](make-bag-if-aggfunction.md)|Retourne un jeu de propriétés de valeurs dynamiques dans le groupe (avec un prédicat)|
|[make_list()](makelist-aggfunction.md)|Retourne une liste de toutes les valeurs dans le groupe|
|[make_list_if()](makelistif-aggfunction.md)|Retourne une liste de toutes les valeurs dans le groupe (avec un prédicat)|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|Retourne une liste de toutes les valeurs dans le groupe, y compris les valeurs null|
|[make_set()](makeset-aggfunction.md)|Retourne un jeu de valeurs distinctes dans le groupe|
|[make_set_if()](makesetif-aggfunction.md)|Retourne un jeu de valeurs distinctes au sein du groupe (avec un prédicat)|
|[max()](max-aggfunction.md)|Retourne la valeur maximale dans l'ensemble du groupe|
|[maxif()](maxif-aggfunction.md)|Retourne la valeur maximale dans l’ensemble du groupe (avec un prédicat)|
|[min()](min-aggfunction.md)|Retourne la valeur minimale dans l'ensemble du groupe|
|[minif()](minif-aggfunction.md)|Retourne la valeur minimale dans l’ensemble du groupe (avec un prédicat)|
|[percentiles()](percentiles-aggfunction.md)|Retourne le centile approximatif du groupe|
|[percentiles_array()](percentiles-aggfunction.md)|Retourne les centiles approximatifs du groupe|
|[percentilesw()](percentiles-aggfunction.md)|Retourne le centile approximatif pondéré du groupe|
|[percentilesw_array()](percentiles-aggfunction.md)|Retourne les centiles approximatifs pondérés du groupe|
|[stdev()](stdev-aggfunction.md)|Retourne l’écart type du groupe|
|[stdevif()](stdevif-aggfunction.md)|Retourne l'écart type du groupe (avec un prédicat)|
|[sum()](sum-aggfunction.md)|Retourne la somme des éléments dans le groupe|
|[sumif()](sumif-aggfunction.md)|Retourne la somme des éléments dans le groupe (avec un prédicat)|
|[variance()](variance-aggfunction.md)|Retourne la variance dans le groupe|
|[varianceif()](varianceif-aggfunction.md)|Retourne la variance dans le groupe (avec un prédicat)|

## <a name="aggregates-default-values"></a>Agrège les valeurs par défaut

Le tableau suivant récapitule les valeurs par défaut des agrégations :

Opérateur       |Valeur par défaut                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    tableau dynamique vide ([])          
 Tous les autres          |   null                           

 Lorsque vous utilisez ces agrégations sur des entités qui incluent des valeurs null, les valeurs nulls sont ignorées et ne participent pas au calcul (voir les exemples ci-dessous).

## <a name="examples"></a>Exemples

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="Résumer le prix par fruit et fournisseur":::

## <a name="example-unique-combination"></a>Exemple : Combinaison unique

Déterminez les combinaisons uniques de `ActivityType` et `CompletionStatus` dans une table. Il n’existe aucune fonction d’agrégation, mais uniquement des clés de regroupement. La sortie affiche simplement les colonnes de ces résultats :

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example-minimum-and-maximum-timestamp"></a>Exemple : Horodatage minimal et maximal

Recherche l’horodatage minimal et maximal de tous les enregistrements dans la table Activities. Comme il n’y a pas de clause group by, la sortie contient une seule ligne :

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example-distinct-count"></a>Exemple : Nombre distinct

Créez une ligne pour chaque continent, en indiquant le nombre de villes dans lesquelles les activités se produisent. Comme il y a peu de valeurs pour « continent », aucune fonction de regroupement n’est nécessaire dans la clause « by » :

```kusto
Activities | summarize cities=dcount(city) by continent
```

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example-histogram"></a>Exemple : Histogramme

L’exemple suivant calcule un histogramme pour chaque type d’activité. Étant donné que `Duration` a de nombreuses valeurs, utilisez `bin` pour regrouper ses valeurs en intervalles de 10 minutes :

```kusto
Activities | summarize count() by ActivityType, length=bin(Duration, 10m)
```

|`count_`|`ActivityType`|`length`
|---:|---|---
|`354`| `dancing` | `0:00:00.000`
|`23`|`singing` | `0:00:00.000`
|`2717`|`dancing`|`0:10:00.000`
|`341`|`singing`|`0:10:00.000`
|`725`|`dancing`|`0:20:00.000`
|`2876`|`singing`|`0:20:00.000`
|...

**Exemple pour les valeurs par défaut des agrégations**

Lorsque l’entrée de l’opérateur `summarize` a au moins une clé de regroupement vide, le résultat est également vide.

Lorsque l’entrée de l’opérateur `summarize` n’a pas de clé de regroupement vide, le résultat inclut les valeurs par défaut des agrégations utilisés dans `summarize` :

```kusto
datatable(x:long)[]
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
datatable(x:long)[]
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
datatable(x:long)[]
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

La moyenne des agrégations calcule la somme de toutes les valeurs non null et compte uniquement celles qui ont participé au calcul (sans les valeurs null).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

Le nombre régulier compte les valeurs null : 

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize count(y)
```

|count_y|
|---|
|2|

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize make_set(y), make_set(y)
```

|set_y|set_y1|
|---|---|
|[5.0]|[5.0]|
