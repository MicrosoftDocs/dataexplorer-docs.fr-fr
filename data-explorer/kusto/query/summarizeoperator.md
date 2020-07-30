---
title: synthèse de l’opérateur-Explorateur de données Azure | Microsoft Docs
description: Cet article décrit l’opérateur de synthèse dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.openlocfilehash: a200d0619b25fe7410a82a941a3b1bf6e35d60ac
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87342612"
---
# <a name="summarize-operator"></a>opérateur summarize

Génère une table qui agrège le contenu de la table d’entrée.

```kusto
T | summarize count(), avg(price) by fruit, supplier
```

Un tableau qui indique le nombre et le prix moyen de chaque fruit de chaque fournisseur. La sortie contient une ligne pour chaque combinaison de fruits et de fournisseurs. Les colonnes de sortie indiquent le nombre, le prix moyen, le fruit et le fournisseur. Toutes les autres colonnes d’entrée sont supprimées.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

Une table indiquant le nombre d’éléments ayant un prix dans chaque intervalle [0,10.0], [10.0,20.0] et ainsi de suite. Cet exemple comprend une colonne pour le nombre et une autre pour la gamme de prix. Toutes les autres colonnes d’entrée sont supprimées.

## <a name="syntax"></a>Syntaxe

*T* `| summarize` [[*Column* `=` ] *Aggregation* [ `,` ...]] [ `by` [*colonne* `=` ] *GroupExpression* [ `,` ...]]

## <a name="arguments"></a>Arguments

* *Column :* nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression.
* *Agrégation :* Appel à une [fonction d’agrégation](summarizeoperator.md#list-of-aggregation-functions) telle que `count()` ou `avg()` , avec les noms de colonnes comme arguments. Voir la [liste des fonctions d’agrégation](summarizeoperator.md#list-of-aggregation-functions).
* *GroupExpression* : expression sur les colonnes, qui fournit un ensemble de valeurs distinctes. En général, il s’agit d’un nom de colonne qui fournit déjà un ensemble restreint de valeurs, ou de `bin()` avec une colonne numérique ou horaire en tant qu’argument. 

> [!NOTE]
> Lorsque la table d’entrée est vide, la sortie varie selon que *GroupExpression* est utilisé :
>
> * Si *GroupExpression* n’est pas fourni, la sortie est une ligne unique (vide).
> * Si *GroupExpression* est fourni, la sortie n’a pas de ligne.

## <a name="returns"></a>Retours

Les lignes d’entrée sont organisées en groupes ayant les mêmes valeurs que les expressions `by` . Ensuite, les fonctions d’agrégation spécifiées sont calculées sur chaque groupe, générant une ligne pour chaque groupe. Le résultat contient les colonnes `by` et au moins une colonne pour chaque agrégation calculée. (Certaines fonctions d’agrégation retournent plusieurs colonnes.)

Le résultat contient autant de lignes qu’il y a de combinaisons de `by` valeurs distinctes (qui peuvent être null). Si aucune clé de groupe n’est fournie, le résultat comporte un seul enregistrement.

Pour résumer des plages de valeurs numériques, utilisez `bin()` pour réduire les plages aux valeurs discrètes.

> [!NOTE]
> * Bien que vous puissiez fournir des expressions arbitraires pour les expressions d’agrégation et de regroupement, il est plus efficace d’utiliser des noms de colonne simples ou d’appliquer `bin()` à une colonne numérique.
> * Les emplacements horaires automatiques pour les colonnes DateTime ne sont plus pris en charge. Utilisez à la place compartimentage explicite. Par exemple : `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>Liste des fonctions d’agrégation

|Fonction|Description|
|--------|-----------|
|[Any ()](any-aggfunction.md)|Retourne une valeur non vide aléatoire pour le groupe|
|[anyif()](anyif-aggfunction.md)|Retourne une valeur non vide aléatoire pour le groupe (prédicat with)|
|[arg_max()](arg-max-aggfunction.md)|Retourne une ou plusieurs expressions lorsque l’argument est agrandi|
|[arg_min()](arg-min-aggfunction.md)|Retourne une ou plusieurs expressions lorsque l’argument est réduit|
|[Moy ()](avg-aggfunction.md)|Retourne une valeur moyenne dans le groupe|
|[avgif()](avgif-aggfunction.md)|Retourne une valeur moyenne dans le groupe (avec le prédicat)|
|[binary_all_and](binary-all-and-aggfunction.md)|Retourne une valeur agrégée à l’aide du binaire `AND` du groupe.|
|[binary_all_or](binary-all-or-aggfunction.md)|Retourne une valeur agrégée à l’aide du binaire `OR` du groupe.|
|[binary_all_xor](binary-all-xor-aggfunction.md)|Retourne une valeur agrégée à l’aide du binaire `XOR` du groupe.|
|[buildschema()](buildschema-aggfunction.md)|Retourne le schéma minimal qui admet toutes les valeurs de l' `dynamic` entrée|
|[Count ()](count-aggfunction.md)|Retourne le nombre de groupes|
|[countif()](countif-aggfunction.md)|Retourne un nombre avec le prédicat du groupe.|
|[dcount()](dcount-aggfunction.md)|Retourne un nombre approximatif distinct des éléments de groupe|
|[dcountif()](dcountif-aggfunction.md)|Retourne un nombre approximatif distinct des éléments de groupe (prédicat with)|
|[make_bag()](make-bag-aggfunction.md)|Retourne un jeu de propriétés de valeurs dynamiques dans le groupe|
|[make_bag_if()](make-bag-if-aggfunction.md)|Retourne un jeu de propriétés de valeurs dynamiques dans le groupe (prédicat with)|
|[make_list()](makelist-aggfunction.md)|Retourne une liste de toutes les valeurs dans le groupe|
|[make_list_if()](makelistif-aggfunction.md)|Retourne une liste de toutes les valeurs dans le groupe (prédicat with)|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|Retourne une liste de toutes les valeurs dans le groupe, y compris les valeurs null|
|[make_set()](makeset-aggfunction.md)|Retourne un jeu de valeurs distinctes dans le groupe|
|[make_set_if()](makesetif-aggfunction.md)|Retourne un jeu de valeurs distinctes au sein du groupe (prédicat with)|
|[max()](max-aggfunction.md)|Retourne la valeur maximale dans l'ensemble du groupe|
|[maxif()](maxif-aggfunction.md)|Retourne la valeur maximale dans le groupe (avec le prédicat)|
|[min()](min-aggfunction.md)|Retourne la valeur minimale dans l'ensemble du groupe|
|[minif()](minif-aggfunction.md)|Retourne la valeur minimale dans le groupe (avec le prédicat)|
|[centile ()](percentiles-aggfunction.md)|Retourne le centile approximatif du groupe|
|[percentiles_array ()](percentiles-aggfunction.md)|Retourne les centiles approximatifs du groupe|
|[percentilesw()](percentiles-aggfunction.md)|Retourne le centile pondéré approximatif du groupe|
|[percentilesw_array ()](percentiles-aggfunction.md)|Retourne les centile pondérés approximatifs du groupe|
|[ECARTYPE ()](stdev-aggfunction.md)|Retourne l’écart type de l’ensemble du groupe|
|[stdevif()](stdevif-aggfunction.md)|Retourne l’écart type de l’ensemble du groupe (avec le prédicat)|
|[Sum ()](sum-aggfunction.md)|Retourne la somme des éléments avec le groupe|
|[sumif()](sumif-aggfunction.md)|Retourne la somme des éléments avec le groupe (prédicat with)|
|[variance()](variance-aggfunction.md)|Retourne la variance dans le groupe|
|[varianceif()](varianceif-aggfunction.md)|Retourne la variance dans le groupe (avec le prédicat)|

## <a name="aggregates-default-values"></a>Agrège les valeurs par défaut

Le tableau suivant récapitule les valeurs par défaut des agrégations :

Opérateur       |Valeur par défaut                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    tableau dynamique vide ([])          
 Tous les autres          |   null                           

 Lorsque vous utilisez ces agrégats sur des entités qui incluent des valeurs NULL, les valeurs NULL sont ignorées et ne participent pas au calcul (voir les exemples ci-dessous).

## <a name="examples"></a>Exemples

:::image type="content" source="images/summarizeoperator/summarize-price-by-supplier.png" alt-text="Résumer le prix par fruit et fournisseur":::

## <a name="example"></a>Exemple

Déterminez les combinaisons uniques de `ActivityType` et de `CompletionStatus` dans une table. Il n’existe aucune fonction d’agrégation, mais uniquement des clés de regroupement. La sortie affiche simplement les colonnes de ces résultats :

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

## <a name="example"></a>Exemple

Recherche l’horodateur minimal et maximal de tous les enregistrements dans la table des activités. Comme il n’y a pas de clause group by, la sortie contient une seule ligne :

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

## <a name="example"></a>Exemple

Créez une ligne pour chaque continent, en indiquant le nombre de villes dans lesquelles les activités se produisent. Comme il y a peu de valeurs pour « continent », aucune fonction de regroupement n’est nécessaire dans la clause « by » :

    Activities | summarize cities=dcount(city) by continent

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


## <a name="example"></a>Exemple

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

**Exemple pour les valeurs par défaut des agrégats**

Lorsque l’entrée de l' `summarize` opérateur a au moins une clé Group-by vide, le résultat est vide.

Lorsque l’entrée de l' `summarize` opérateur n’a pas de clé Group-by vide, les valeurs par défaut des agrégats utilisés dans le sont les `summarize` suivantes :

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

L’agrégat calcule la somme de toutes les valeurs non null et compte uniquement celles qui ont participé au calcul (n’acceptent pas les valeurs NULL en compte).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

Le nombre régulier compte les valeurs NULL : 

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
|[5,0]|[5,0]|
