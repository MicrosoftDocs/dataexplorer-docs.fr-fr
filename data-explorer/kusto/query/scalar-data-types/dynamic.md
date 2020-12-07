---
title: Type de données dynamiques – Azure Data Explorer | Microsoft Docs
description: Cet article décrit le type de données dynamiques dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 582683a9261d84fa24d819b5234e58effaf90a97
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512024"
---
# <a name="the-dynamic-data-type"></a>Le type de données dynamiques

Le type de données scalaires `dynamic` est spécial dans la mesure où il peut prendre n’importe quelle valeur d’autres types de données scalaires de la liste ci-dessous, ainsi que des tableaux et des jeux de propriétés. Plus précisément, une valeur `dynamic` peut être :

* Null.
* Valeur de l’un des types de données scalaires primitifs : `bool`, `datetime`, `guid`, `int`, `long`, `real`, `string` et `timespan`.
* Tableau de valeurs `dynamic`, contenant zéro ou plusieurs valeurs avec une indexation de base zéro.
* Jeu de propriétés qui mappe des valeurs `string` uniques à des valeurs `dynamic`.
  Le jeu de propriétés comporte zéro, un ou plusieurs de ces mappages (appelés « emplacements »), indexés par les valeurs `string` uniques. Les emplacements ne sont pas ordonnés.

> [!NOTE]
> * Les valeurs de type `dynamic` sont limitées à 1 Mo (2^20).
> * Bien que le type `dynamic` apparaisse au format JSON, il peut contenir des valeurs qui ne sont pas représentées par le modèle JSON, car elles n’existent pas dans JSON (par exemple, `long`, `real`, `datetime`, `timespan` et `guid`).
>   Par conséquent, lors de la sérialisation de valeurs `dynamic` dans une représentation JSON, les valeurs que JSON ne peut pas représenter sont sérialisées en valeurs `string`. À l’inverse, Kusto analyse les chaînes en tant que valeurs fortement typées si elles peuvent être analysées en tant que telles.
>   Cela s’applique aux types `datetime`, `real`, `long` et `guid`. 
>   Pour plus d’informations sur le modèle objet JSON, consultez [json.org](https://json.org/).
> * Kusto ne tente pas de conserver l’ordre des mappages de nom à valeur dans un jeu de propriétés, et vous ne pouvez donc pas supposer que l’ordre est conservé. Il est tout à fait possible pour deux jeux de propriétés avec le même jeu de mappages de produire des résultats différents lorsqu’ils sont représentés en tant que valeurs `string`, par exemple.

## <a name="dynamic-literals"></a>Littéraux dynamiques

Un littéral de type `dynamic` se présente comme suit :

`dynamic(` *valeur* `)`

La *valeur* peut être la suivante :

* `null`, auquel cas le littéral représente la valeur dynamique null : `dynamic(null)`.
* Autre littéral de type de données scalaires, auquel cas le littéral représente le littéral `dynamic` du type « inner ». Par exemple, `dynamic(4)` est une valeur dynamique contenant la valeur 4 du type de données scalaires long.
* Tableau de littéraux dynamiques ou autres : `[` *ListOfValues* `]`. Par exemple, `dynamic([1, 2, "hello"])` est un tableau dynamique de trois éléments, deux valeurs `long` et une valeur `string`.
* Un jeu de propriétés : `{` *Nom* `=` *Valeur* ... `}`. Par exemple, `dynamic({"a":1, "b":{"a":2}})` est un jeu de propriétés avec deux emplacements, `a`et `b`, le deuxième emplacement étant un autre jeu de propriétés.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

Pour plus de commodité, les littéraux `dynamic` qui apparaissent dans le texte de la requête peuvent également inclure d’autres littéraux Kusto avec les types suivants : `datetime`, `timespan`, `real`, `long`, `guid`, `bool` et `dynamic`.
Cette extension sur JSON n’est pas disponible lors de l’analyse de chaînes (par exemple, lors de l’utilisation de la fonction `parse_json` ou lors de l’ingestion de données), mais elle vous permet d’effectuer les opérations suivantes :

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

Pour analyser une valeur `string` qui suit les règles d’encodage JSON dans une valeur `dynamic`, utilisez la fonction `parse_json`. Par exemple :

* `parse_json('[43, 21, 65]')` : tableau de nombres
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` : dictionnaire
* `parse_json('21')` : valeur unique de type dynamique qui contient un nombre
* `parse_json('"21"')` : valeur unique de type dynamique qui contient une chaîne
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` : donne la même valeur que `o` dans l’exemple ci-dessus.

> [!NOTE]
> Contrairement à JavaScript, JSON impose l’utilisation de caractères à guillemet double (`"`) autour des chaînes et des noms de propriété de jeu de propriétés.
> Ainsi, il est généralement plus facile de placer les littéraux de chaîne JSON entre des apostrophes (`'`).
  
L’exemple suivant montre comment vous pouvez définir une table qui contient une colonne `dynamic` (ainsi qu’une colonne `datetime`), puis l’ingérer en un seul enregistrement. Il montre également comment vous pouvez encoder des chaînes JSON dans des fichiers CSV :

```kusto
// dynamic is just like any other type:
.create table Logs (Timestamp:datetime, Trace:dynamic)

// Everything between the "[" and "]" is parsed as a CSV line would be:
// 1. Since the JSON string includes double-quotes and commas (two characters
//    that have a special meaning in CSV), we must CSV-quote the entire second field.
// 2. CSV-quoting means adding double-quotes (") at the immediate beginning and end
//    of the field (no spaces allowed before the first double-quote or after the second
//    double-quote!)
// 3. CSV-quoting also means doubling-up every instance of a double-quotes within
//    the contents.
.ingest inline into table Logs
  [2015-01-01,"{""EventType"":""Demo"", ""EventValue"":""Double-quote love!""}"]
```

|Timestamp                   | Trace                                                 |
|----------------------------|-------------------------------------------------------|
|2015-01-01 00:00:00.0000000 | {"EventType":"Demo","EventValue":"Double-quote love!"}|

## <a name="dynamic-object-accessors"></a>Accesseurs d’objets dynamiques

Pour mettre un dictionnaire en indice, utilisez la notation en points (`dict.key`) ou la notation en crochets (`dict["key"]`).
Lorsque l’indice est une constante de chaîne, les deux options sont équivalentes.

> [!NOTE] 
> Pour utiliser une expression comme indice, utilisez la notation en crochets. Lorsque vous utilisez des expressions arithmétiques, l’expression à l’intérieur des crochets doit être placée entre parenthèses.

Dans les exemples ci-dessous, `dict` et `arr` sont des colonnes de type dynamique :

|Expression                        | Type d’expression d’accesseur | Signification                                                                              | Commentaires                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | Nom de l’entité (colonne)     | Met en indice un dictionnaire à l’aide des valeurs de la colonne `col` comme clé              | La colonne doit être de type chaîne                 | 
|arr[index]                        | Index d’entité (colonne)    | Met en indice un tableau à l’aide des valeurs de la colonne `index` comme index              | La colonne doit être de type entier ou booléen     | 
|arr[-index]                       | Index d’entité (colonne)    | Récupère la valeur « index-th » à partir de la fin du tableau                             | La colonne doit être de type entier ou booléen     |
|arr[(-1)]                         | Index d’entité             | Récupère la dernière valeur dans le tableau                                                |                                               |
|arr[toint(indexAsString)]         | Appel de fonction            | Effectue un cast des valeurs de la colonne `indexAsString` en valeur int et les utilise pour mettre en indice un tableau |                                               |
|dict[['where']]                   | Mot clé utilisé comme nom d’entité (colonne) | Met en indice un dictionnaire à l’aide des valeurs de la colonne `where` comme clé    | Les noms d’entité identiques à certains mots clés de langage de requête doivent être placés entre guillemets. | 
|dict.['where'] ou dict['where']   | Constante                 | Met en indice un dictionnaire à l’aide de la chaîne `where` comme clé                              |                                               |

**Conseil relatif aux performances :** utilisez de préférence des indices constants lorsque cela est possible

L’accès à un sous-objet d’une valeur `dynamic` produit une autre valeur `dynamic`, même si le sous-objet a un type sous-jacent différent. Utilisez la fonction `gettype` pour découvrir le type sous-jacent réel de la valeur et l’une des fonctions cast listées ci-dessous pour effectuer un cast vers le type réel.

## <a name="casting-dynamic-objects"></a>Cast d’objets dynamiques

> Après avoir mis en indice un objet dynamique, vous devez effectuer un cast de la valeur en un type simple.

|Expression | Valeur | Type|
|---|---|---|
| X | parse_json('[100,101,102]')| tableau|
|X[0]|parse_json('100')|dynamique|
|toint(X[1])|101| int|
| Y | parse_json('{"a1":100, "a b c":"2015-01-01"}')| dictionnaire|
|Y.a1|parse_json('100')|dynamique|
|Y["a b c"]| parse_json("2015-01-01")|dynamique|
|todate(Y["a b c"])|datetime(2015-01-01)| DATETIME|

Les fonctions cast sont les suivantes :

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>Génération d’objets dynamiques

Plusieurs fonctions vous permettent de créer des objets `dynamic` :

* [pack()](../packfunction.md) crée un jeu de propriétés à partir de paires nom/valeur.
* [pack_array()](../packarrayfunction.md) crée un tableau à partir de paires nom/valeur.
* [range()](../rangefunction.md) crée un tableau avec une série arithmétique de nombres.
* [zip()](../zipfunction.md) appaire les valeurs « parallèles » de deux tableaux en un seul tableau.
* [repeat()](../repeatfunction.md) crée un tableau avec une valeur répétée.

En outre, il existe plusieurs fonctions d’agrégation qui créent des tableaux `dynamic` pour contenir des valeurs agrégées :

* [buildschema()](../buildschema-aggfunction.md) retourne le schéma d’agrégation de plusieurs valeurs `dynamic`.
* [make_bag()](../make-bag-aggfunction.md) retourne un jeu de propriétés de valeurs dynamiques dans le groupe.
* [make_bag_if()](../make-bag-if-aggfunction.md) retourne un jeu de propriétés de valeurs dynamiques dans le groupe (avec un prédicat).
* [make_list()](../makelist-aggfunction.md) retourne un tableau contenant toutes les valeurs, dans l’ordre.
* [make_list_if()](../makelistif-aggfunction.md) retourne un tableau contenant toutes les valeurs, dans l’ordre (avec un prédicat).
* [make_list_with_nulls()](../make-list-with-nulls-aggfunction.md) retourne un tableau contenant toutes les valeurs, dans l’ordre, y compris les valeurs null.
* [make_set()](../makeset-aggfunction.md) retourne un tableau contenant toutes les valeurs uniques.
* [make_set_if()](../makesetif-aggfunction.md) retourne un tableau contenant toutes les valeurs uniques (avec un prédicat).

## <a name="operators-and-functions-over-dynamic-types"></a>Opérateurs et fonctions sur des types dynamiques

|Opérateur ou fonction|Utilisation avec des types de données dynamiques|
|---|---|
| *value* `in` *array*| True s’il existe un élément de *array* qui est égal à *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True s’il n’existe aucun élément de *array* qui est égal à *value*
|[`array_length(`array`)`](../arraylengthfunction.md)| Null si ce n’est pas un tableau
|[`bag_keys(`bag`)`](../bagkeysfunction.md)| Énumère toutes les clés racines dans un objet de jeu de propriétés dynamique.
|[`bag_merge(`bag1,...,bagN`)`](../bag-merge-function.md)| Fusionne des jeux de propriétés dynamiques dans un jeu de propriétés dynamique avec toutes les propriétés fusionnées.
|[`extractjson(`path,object`)`](../extractjsonfunction.md)|Utilise le chemin pour accéder à l’objet.
|[`parse_json(`source`)`](../parsejsonfunction.md)| Convertit une chaîne JSON en un objet dynamique.
|[`range(`from,to,step`)`](../rangefunction.md)| Tableau de valeurs
|[`mv-expand` listColumn](../mvexpandoperator.md) | Réplique une ligne pour chaque valeur d’une liste dans une cellule spécifiée.
|[`summarize buildschema(`column`)`](../buildschema-aggfunction.md) |Déduit le schéma du type à partir du contenu de la colonne
|[`summarize make_bag(`column`)`](../make-bag-aggfunction.md) | Fusionne les valeurs du jeu de propriétés (dictionnaire) de la colonne dans un jeu de propriétés, sans duplication de clé.
|[`summarize make_bag_if(`column,predicate`)`](../make-bag-if-aggfunction.md) | Fusionne les valeurs du jeu de propriétés (dictionnaire) de la colonne dans un jeu de propriétés, sans duplication de clé (avec un prédicat).
|[`summarize make_list(`column`)` ](../makelist-aggfunction.md)| Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau.
|[`summarize make_list_if(`column,predicate`)` ](../makelistif-aggfunction.md)| Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau (avec un prédicat).
|[`summarize make_list_with_nulls(`column`)` ](../make-list-with-nulls-aggfunction.md)| Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau, y compris les valeurs null.
|[`summarize make_set(`column`)`](../makeset-aggfunction.md) | Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau, sans duplication.
