---
title: résumé opérateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de résumé dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/20/2020
ms.openlocfilehash: ed1808f173d0f779c84f9405987d7395de833120
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663218"
---
# <a name="summarize-operator"></a>opérateur summarize

Génère une table qui agrège le contenu de la table d’entrée.

```kusto
T | summarize count(), avg(price) by fruit, supplier
```

Un tableau qui montre le nombre et le prix moyen de chaque fruit de chaque fournisseur. Il ya une rangée dans la sortie pour chaque combinaison distincte de fruits et de fournisseur. Les colonnes de sortie montrent le nombre, le prix moyen, les fruits et le fournisseur. Toutes les autres colonnes d’entrée sont supprimées.

```kusto
T | summarize count() by price_range=bin(price, 10.0)
```

Une table indiquant le nombre d’éléments ayant un prix dans chaque intervalle [0,10.0], [10.0,20.0] et ainsi de suite. Cet exemple comprend une colonne pour le nombre et une autre pour la gamme de prix. Toutes les autres colonnes d’entrée sont supprimées.

**Syntaxe**

*T* `| summarize` [[*Colonne* `=`]`,` *Agrégation* [...]] [`by` [*Colonne* `=`] *GroupExpression* [...]]`,`

**Arguments**

* *Column :* nom facultatif d’une colonne de résultats. Prend par défaut un nom dérivé de l’expression.
* *Agrégation:* Un appel à une [fonction d’agrégation](summarizeoperator.md#list-of-aggregation-functions) telle que `count()` ou `avg()`, avec des noms de colonne comme arguments. Voir la [liste des fonctions d’agrégation](summarizeoperator.md#list-of-aggregation-functions).
* *GroupExpression* : expression sur les colonnes, qui fournit un ensemble de valeurs distinctes. En général, il s’agit d’un nom de colonne qui fournit déjà un ensemble restreint de valeurs, ou de `bin()` avec une colonne numérique ou horaire en tant qu’argument. 

> [!NOTE]
> Lorsque le tableau d’entrée est vide, la sortie dépend de *l’utilisation de GroupExpression* :
>
> * Si *GroupExpression n’est* pas fourni, la sortie sera une seule ligne (vide).
> * Si *GroupExpression* est fourni, la sortie n’aura pas de lignes.

**Retourne**

Les lignes d’entrée sont organisées en groupes ayant les mêmes valeurs que les expressions `by` . Ensuite, les fonctions d’agrégation spécifiées sont calculées sur chaque groupe, générant une ligne pour chaque groupe. Le résultat contient les colonnes `by` et au moins une colonne pour chaque agrégation calculée. (Certaines fonctions d’agrégation retournent plusieurs colonnes.)

Le résultat a autant de lignes qu’il y a des combinaisons distinctes de `by` valeurs (qui peuvent être nulles). S’il n’y a pas de clés de groupe fournies, le résultat a un seul enregistrement.

Pour résumer sur les gammes `bin()` de valeurs numériques, utilisez-le pour réduire les plages à des valeurs discrètes.

> [!NOTE]
> * Bien que vous puissiez fournir des expressions arbitraires pour les expressions d’agrégation et de regroupement, il est plus efficace d’utiliser des noms de colonne simples ou d’appliquer `bin()` à une colonne numérique.
> * Les bacs horaires automatiques pour les colonnes de date n’est plus pris en charge. Utilisez plutôt le binning explicite. Par exemple : `summarize by bin(timestamp, 1h)`.

## <a name="list-of-aggregation-functions"></a>Liste des fonctions d’agrégation

|Fonction|Description|
|--------|-----------|
|[n’importe(importe))](any-aggfunction.md)|Retourne une valeur aléatoire non vide pour le groupe|
|[anyif()](anyif-aggfunction.md)|Retourne une valeur aléatoire non vide pour le groupe (avec prédicat)|
|[arg_max()](arg-max-aggfunction.md)|Renvoie une ou plusieurs expressions lorsque l’argument est maximisé|
|[arg_min()](arg-min-aggfunction.md)|Renvoie une ou plusieurs expressions lorsque l’argument est minimisé|
|[avg()](avg-aggfunction.md)|Rendements d’une valeur moyenne dans l’ensemble du groupe|
|[avgif()](avgif-aggfunction.md)|Rendements d’une valeur moyenne dans l’ensemble du groupe (avec prédicat)|
|[binary_all_and](binary-all-and-aggfunction.md)|Retourne la valeur agrégée à l’aide du binaire `AND` du groupe|
|[binary_all_or](binary-all-or-aggfunction.md)|Retourne la valeur agrégée à l’aide du binaire `OR` du groupe|
|[binary_all_xor](binary-all-xor-aggfunction.md)|Retourne la valeur agrégée à l’aide du binaire `XOR` du groupe|
|[buildschema()](buildschema-aggfunction.md)|Retourne le schéma minimal qui admet `dynamic` toutes les valeurs de l’entrée|
|[compter()](count-aggfunction.md)|Retourne un décompte du groupe|
|[countif()](countif-aggfunction.md)|Retourne un compte avec le prédicat du groupe|
|[dcount()](dcount-aggfunction.md)|Renvoie un nombre distinct approximatif des éléments du groupe|
|[dcountif()](dcountif-aggfunction.md)|Renvoie un nombre distinct approximatif des éléments du groupe (avec prédicat)|
|[make_bag()](make-bag-aggfunction.md)|Retourne un sac de propriété de valeurs dynamiques au sein du groupe|
|[make_bag_if()](make-bag-if-aggfunction.md)|Retourne un sac de propriété de valeurs dynamiques au sein du groupe (avec prédicat)|
|[make_list()](makelist-aggfunction.md)|Retourne une liste de toutes les valeurs au sein du groupe|
|[make_list_if()](makelistif-aggfunction.md)|Retourne une liste de toutes les valeurs au sein du groupe (avec prédicat)|
|[make_list_with_nulls()](make-list-with-nulls-aggfunction.md)|Retourne une liste de toutes les valeurs au sein du groupe, y compris les valeurs nulles|
|[make_set()](makeset-aggfunction.md)|Retourne un ensemble de valeurs distinctes au sein du groupe|
|[make_set_if()](makesetif-aggfunction.md)|Retourne un ensemble de valeurs distinctes au sein du groupe (avec prédicat)|
|[max()](max-aggfunction.md)|Retourne la valeur maximale dans l’ensemble du groupe|
|[maxif()](maxif-aggfunction.md)|Retourne la valeur maximale dans l’ensemble du groupe (avec prédicat)|
|[min()](min-aggfunction.md)|Retourne la valeur minimale dans l’ensemble du groupe|
|[minif()](minif-aggfunction.md)|Retourne la valeur minimale dans l’ensemble du groupe (avec prédicat)|
|[percentiles()](percentiles-aggfunction.md)|Retourne l’approximatif percentile du groupe|
|[percentiles_array()](percentiles-aggfunction.md)|Retourne les centiles approximatifs du groupe|
|[percentilesw ()](percentiles-aggfunction.md)|Retourne l’approximatif percentile pondéré du groupe|
|[percentilesw_array()](percentiles-aggfunction.md)|Retourne les percentiles pondérés approximatifs du groupe|
|[stdev()](stdev-aggfunction.md)|Retourne l’écart standard dans l’ensemble du groupe|
|[stdevif()](stdevif-aggfunction.md)|Retourne l’écart standard à travers le groupe (avec prédicat)|
|[sum()](sum-aggfunction.md)|Retourne la somme des éléments avec le groupe|
|[sumif()](sumif-aggfunction.md)|Retourne la somme des éléments avec le groupe (avec prédicat)|
|[variance()](variance-aggfunction.md)|Retourne l’écart à travers le groupe|
|[varianceif()](varianceif-aggfunction.md)|Retourne la variance à travers le groupe (avec prédicate)|

## <a name="aggregates-default-values"></a>Agrège les valeurs par défaut

Le tableau suivant résume les valeurs par défaut des agrégations :

Opérateur       |Valeur par défaut                         
---------------|------------------------------------
 `count()`, `countif()`, `dcount()`, `dcountif()`         |   0                            
 `make_bag()`, `make_bag_if()`, `make_list()`, `make_list_if()`, `make_set()`, `make_set_if()` |    tableau dynamique vide ([])          
 Tous les autres          |   null                           

 Lors de l’utilisation de ces agrégats sur les entités qui comprend des valeurs nulles, les valeurs nulles seront ignorées et ne participeront pas au calcul (voir les exemples ci-dessous).

## <a name="examples"></a>Exemples

![texte de remplacement](./Images/aggregations/01.png "01")

**Exemple**

Déterminez quelles combinaisons `CompletionStatus` uniques et `ActivityType` il y a dans un tableau. Il n’y a pas de fonctions d’agrégation, juste des clés de groupe. La sortie affichera simplement les colonnes pour ces résultats:

```kusto
Activities | summarize by ActivityType, completionStatus
```

|`ActivityType`|`completionStatus`
|---|---
|`dancing`|`started`
|`singing`|`started`
|`dancing`|`abandoned`
|`singing`|`completed`

**Exemple**

Trouve le délai minimum et maximal de tous les enregistrements dans le tableau des activités. Comme il n’y a pas de clause group by, la sortie contient une seule ligne :

```kusto
Activities | summarize Min = min(Timestamp), Max = max(Timestamp)
```

|`Min`|`Max`
|---|---
|`1975-06-09 09:21:45` | `2015-12-24 23:45:00`

**Exemple**

Créer une rangée pour chaque continent, montrant un compte des villes dans lesquelles les activités se produisent. Étant donné qu’il y a peu de valeurs pour le « continent », aucune fonction de regroupement n’est nécessaire dans la clause « par » :

    Activities | summarize cities=dcount(city) by continent

|`cities`|`continent`
|---:|---
|`4290`|`Asia`|
|`3267`|`Europe`|
|`2673`|`North America`|


**Exemple**

L’exemple suivant calcule un histogramme pour chaque type d’activité. Parce `Duration` que a `bin` de nombreuses valeurs, utiliser pour regrouper ses valeurs en intervalles de 10 minutes:

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

**Exemple pour les valeurs par défaut d’agrégats**

Lorsque l’entrée de l’opérateur `summarize` a au moins une clé de groupe vide par groupe, son résultat est vide, aussi.

Lorsque l’entrée de `summarize` l’opérateur n’a pas de clé de groupe vide, `summarize`le résultat est les valeurs par défaut des agrégats utilisés dans le :

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize any(x), arg_max(x, x), arg_min(x, x), avg(x), buildschema(todynamic(tostring(x))), max(x), min(x), percentile(x, 55), hll(x) ,stdev(x), sum(x), sumif(x, x > 0), tdigest(x), variance(x)
```

|any_x|max_x|max_x_x|min_x|min_x_x|avg_x|schema_x|max_x1|min_x1|percentile_x_55|hll_x|stdev_x|sum_x|sumif_x|tdigest_x|variance_x|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|||||||||||||||||

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  count(x), countif(x > 0) , dcount(x), dcountif(x, x > 0)
```

|count_x|countif_|dcount_x|dcountif_x|
|---|---|---|---|
|0|0|0|0|

```kusto
range x from 1 to 10 step 1
| where 1 == 2
| summarize  make_set(x), make_list(x)
```

|set_x|list_x|
|---|---|
|[]|[]|

L’avg agrégé résume tous les non-nulls et ne compte que ceux qui ont participé au calcul (ne tiendra pas compte des nuls).

```kusto
range x from 1 to 2 step 1
| extend y = iff(x == 1, real(null), real(5))
| summarize sum(y), avg(y)
```

|sum_y|avg_y|
|---|---|
|5|5|

Le décompte régulier comptera les nuls : 

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
