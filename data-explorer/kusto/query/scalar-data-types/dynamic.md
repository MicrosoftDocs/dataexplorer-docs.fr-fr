---
title: Le type de données dynamique - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le type de données dynamique dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e1cdb6e5af20b326198a7447c50c24e5f632d237
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509875"
---
# <a name="the-dynamic-data-type"></a>Le type de données dynamique

Le `dynamic` type de données scalaire est spécial en ce qu’il peut prendre n’importe quelle valeur d’autres types de données scalaires de la liste ci-dessous, ainsi que des tableaux et des sacs de propriété. Plus précisément, une `dynamic` valeur peut être :

* Null.
* Une valeur de l’un des types `bool` `datetime`de `guid` `int`données `long` `real`scalaires primitives: , , , , , , `string`, et `timespan`.
* Un éventail `dynamic` de valeurs, détenant des valeurs nulles ou plus avec indexation à base nulle.
* Un sac de `string` propriété `dynamic` qui cartographie les valeurs uniques aux valeurs.
  Le sac de propriété a zéro ou plus de telles cartes `string` (appelées «slots»), indexé par les valeurs uniques. Les machines à sous ne sont pas ordonnées.

> [!NOTE]
> Les valeurs `dynamic` de type sont limitées à 1 Mo (2-20).

> [!NOTE]
> Bien `dynamic` que le type semble JSON-like, il peut contenir des valeurs que le modèle JSON ne `long` `real`représente `datetime` `timespan`pas `guid`parce qu’ils n’existent pas dans JSON (par exemple, , , , , et ).
> Par conséquent, `dynamic` dans la sérialisation des valeurs dans une représentation JSON, les valeurs que JSON ne peut pas représenter sont sérialisées en `string` valeurs. Inversement, Kusto analysera les chaînes en tant que valeurs fortement typées si elles peuvent être analysées en tant que telles.
> Cela s’applique `long`pour `guid` `datetime`, `real`, et les types. Pour en savoir plus sur le modèle d’objet JSON, voir Voir [json.org](https://json.org/).

> [!NOTE]
> Kusto ne tente pas de préserver l’ordre des cartes de nom à valeur ajoutée dans un sac de propriété, et donc vous ne pouvez pas supposer que l’ordre doit être conservé. Il est tout à fait possible pour deux sacs de propriété avec le `string` même ensemble de cartes de donner des résultats différents quand ils sont représentés comme des valeurs, par exemple.

## <a name="dynamic-literals"></a>Littéralement dynamiques

Un littéral `dynamic` de type ressemble à ceci:

`dynamic(`*Valeur*`)`

*La valeur* peut être :

* `null`, auquel cas le littéral représente `dynamic(null)`la valeur dynamique nulle: .
* Un autre type de données scalaires littérale, `dynamic` auquel cas le littéral représente le littéral du type "intérieur". Par exemple, `dynamic(4)` est une valeur dynamique détenant la valeur 4 du type de données scalaire longue.
* Un éventail de dynamiques ou `[` d’autres littérals: *ListOfValues* `]`. Par exemple, `dynamic([1, 2, "hello"])` est un tableau dynamique `long` de `string` trois éléments, deux valeurs et une valeur.
* Un sac `{` de propriété: *Valeur* *du nom* `=` ... `}`. Par exemple, `dynamic({"a":1, "b":{"a":2}})` est un sac de `a`propriété `b`avec deux fentes, , et , avec la deuxième fente étant un autre sac de propriété.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

Pour plus `dynamic` de commodité, les littérals qui apparaissent dans le texte de `datetime` requête lui-même peuvent également inclure d’autres littérals Kusto (tels que les littérals, `timespan` les littérals, etc.) Cette extension sur JSON n’est pas disponible lors de `parse_json` l’analyse des chaînes (comme lors de l’utilisation de la fonction ou lors de l’ingestion de données), mais il vous permet de le faire:

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

Pour analyser une `string` valeur qui suit les règles `dynamic` d’encodage JSON dans une valeur, utilisez la `parse_json` fonction. Par exemple :

* `parse_json('[43, 21, 65]')` : tableau de nombres
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')`- un dictionnaire
* `parse_json('21')` : valeur unique de type dynamique qui contient un nombre
* `parse_json('"21"')` : valeur unique de type dynamique qui contient une chaîne
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')`- donne la `o` même valeur que dans l’exemple ci-dessus.

> [!NOTE]
> Contrairement à JavaScript, JSON exige l’utilisation de caractères à double citation ()`"`autour des cordes et des noms de propriété-sac.
> Par conséquent, il est généralement plus facile de citer une chaîne encodé JSON littéralement en utilisant un seul-citation (`'`) caractère.
  
L’exemple suivant montre comment vous pouvez `dynamic` définir une table `datetime` qui contient une colonne (ainsi qu’une colonne) et puis y iningérer un seul enregistrement. il montre également comment vous pouvez coder les chaînes JSON dans les fichiers CSV :

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
|2015-01-01 00:00:00.0000000 | "EventType":"Demo","EventValue":"Double-citation amour!"|

## <a name="dynamic-object-accessors"></a>Accesseurs d’objets dynamiques

Pour sous-scripturer un dictionnaire,`dict.key`utilisez soit la`dict["key"]`notation point () ou la notation des parenthèses ().
Lorsque le sous-scriptum est une constante de chaîne, les deux options sont équivalentes.

> [!NOTE] 
> Pour utiliser une expression comme sous-scriptum, utilisez la notation des supports. Lors de l’utilisation d’expressions arithmétiques, l’expression à l’intérieur des parenthèses doit être enveloppée entre parenthèses.

Dans les `dict` exemples ci-dessous et `arr` sont des colonnes de type dynamique:

|Expression                        | Type d’expression d’accesseur | Signification                                                                              | Commentaires                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict[col]                         | Nom de l’entité (colonne)     | Sous-scripte un dictionnaire en `col` utilisant les valeurs de la colonne comme clé              | Colonne doit être de la chaîne de type                 | 
|arr[index]                        | Indice de l’entité (colonne)    | Sous-scripte un tableau utilisant `index` les valeurs de la colonne comme l’index              | Colonne doit être de type integer ou boolean     | 
|arr[-index]                       | Indice de l’entité (colonne)    | Récupère la valeur 'index'-e de la fin du tableau                             | Colonne doit être de type integer ou boolean     |
|arr[(-1)]                         | Indice de l’entité             | Récupère la dernière valeur du tableau                                                |                                               |
|arr[toint(indexAsString)]         | Appel de fonction             | Casts les valeurs `indexAsString` de colonne à int et les utiliser pour sous-scripturer un tableau |                                               |
|dict['où']]                   | Mot-clé utilisé comme nom d’entité (colonne) | Sous-scripte un dictionnaire en `where` utilisant les valeurs de la colonne comme clé    | Les noms d’entités identiques à certains mots clés de langage de requête doivent être cités | 
|dict.['where'] ou dict['where']   | Constant                 | Sous-scripte un `where` dictionnaire utilisant la chaîne comme clé                              |                                               |

**Astuce de performance:** Préférez utiliser des sous-scriptums constants lorsque c’est possible

L’accès à un `dynamic` sous-objet `dynamic` d’une valeur donne une autre valeur, même si le sous-objet a un type sous-jacent différent. Utilisez `gettype` la fonction pour découvrir le type sous-jacent réel de la valeur, et n’importe quelle fonction de distribution énumérée ci-dessous pour la jeter au type réel.

## <a name="casting-dynamic-objects"></a>Casting d’objets dynamiques

> Après avoir sous-scription d’un objet dynamique, vous devez jeter la valeur à un type simple.

|Expression | Valeur | Type|
|---|---|---|
| X | parse_json ('[100,101,102]')| tableau|
|X[0]|parse_json ('100')|dynamique|
|toint(X[1])|101| int|
| O | parse_json ('a1":100, "a b c":"2015-01-01"')| dictionnaire|
|Y.a1|parse_json ('100')|dynamique|
|Y["a b c"]| parse_json ("2015-01-01")|dynamique|
|todate (Y["a b c"])|date(2015-01-01)| DATETIME|

Les fonctions de distribution sont les :

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>Construire des objets dynamiques

Plusieurs fonctions vous permettent `dynamic` de créer de nouveaux objets :

* [emballer)crée](../packfunction.md) un sac de propriété à partir de paires de nom/valeur.
* [pack_array)crée](../packarrayfunction.md) un tableau à partir de paires de noms/valeurs.
* [gamme()](../rangefunction.md) crée un tableau avec une série de nombres arithmétiques.
* [zip ()](../zipfunction.md) paires de valeurs «parallèles» de deux tableaux en un seul tableau.
* [répéter()](../repeatfunction.md) crée un tableau avec une valeur répétée.

En outre, il existe plusieurs `dynamic` fonctions agrégées qui créent des tableaux pour contenir des valeurs agrégées :

* [buildschema()](../buildschema-aggfunction.md) renvoie le schéma `dynamic` agrégé de valeurs multiples.
* [make_bag)retourne](../make-bag-aggfunction.md) un sac de propriété de valeurs dynamiques au sein du groupe.
* [make_bag_if)retourne](../make-bag-if-aggfunction.md) un sac de propriété de valeurs dynamiques au sein du groupe (avec un prédicat).
* [make_list)renvoie](../makelist-aggfunction.md) un tableau contenant toutes les valeurs, dans l’ordre.
* [make_list_if)renvoie](../makelistif-aggfunction.md) un tableau tenant toutes les valeurs, dans l’ordre (avec un prédicat).
* [make_list_with_nulls)renvoie](../make-list-with-nulls-aggfunction.md) un tableau contenant toutes les valeurs, dans l’ordre, y compris les valeurs nulles.
* [make_set)retourne](../makeset-aggfunction.md) un tableau contenant toutes les valeurs uniques.
* [make_set_if)retourne](../makesetif-aggfunction.md) un tableau contenant toutes les valeurs uniques (avec un prédicat).

## <a name="operators-and-functions-over-dynamic-types"></a>Opérateurs et fonctions sur des types dynamiques

|||
|---|---|
| *value* `in` *array*| True s’il existe un élément de *array* qui est égal à *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True s’il n’existe aucun élément de *array* qui est égal à *value*
|[`array_length(`array`)`](../arraylengthfunction.md)| Null si ce n’est pas un tableau
|[`bag_keys(`Sac`)`](../bagkeysfunction.md)| Énumère toutes les touches de racine dans un objet dynamique de sac de propriété.
|[`extractjson(`chemin, objet`)`](../extractjsonfunction.md)|Utilise le chemin pour accéder à l’objet.
|[`parse_json(`Source`)`](../parsejsonfunction.md)| Convertit une chaîne JSON en un objet dynamique.
|[`range(`de, à, étape`)`](../rangefunction.md)| Tableau de valeurs
|[`mv-expand`listeColumn](../mvexpandoperator.md) | Réplique une ligne pour chaque valeur d’une liste dans une cellule spécifiée.
|[`summarize buildschema(`Colonne`)`](../buildschema-aggfunction.md) |Déduit le schéma du type à partir du contenu de la colonne
|[`summarize make_bag(`Colonne`)`](../make-bag-aggfunction.md) | Fusionne les valeurs du sac de propriété (dictionnaire) dans la colonne dans un sac de propriété, sans duplication clé.
|[`summarize make_bag_if(`colonne, prédicate`)`](../make-bag-if-aggfunction.md) | Fusionne les valeurs du sac de propriété (dictionnaire) dans la colonne dans un sac de propriété, sans duplication clé (avec prédicat).
|[`summarize make_list(`colonne`)`](../makelist-aggfunction.md)| Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau.
|[`summarize make_list_if(`colonne, prédicate`)`](../makelistif-aggfunction.md)| Aplatit les groupes de lignes et met les valeurs de la colonne dans un tableau (avec prédicate).
|[`summarize make_list_with_nulls(`colonne`)`](../make-list-with-nulls-aggfunction.md)| Aplatit les groupes de lignes et met les valeurs de la colonne dans un tableau, y compris des valeurs nulles.
|[`summarize make_set(`Colonne`)`](../makeset-aggfunction.md) | Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau, sans duplication.
|[`summarize make_bag(`Colonne`)`](../make-bag-aggfunction.md) | Fusionne les valeurs du sac de propriété (dictionnaire) dans la colonne dans un sac de propriété, sans duplication clé.