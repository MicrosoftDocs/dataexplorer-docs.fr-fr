---
title: Type de données dynamiques-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le type de données dynamiques dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 07/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 582683a9261d84fa24d819b5234e58effaf90a97
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512024"
---
# <a name="the-dynamic-data-type"></a>Le type de données Dynamic

Le `dynamic` type de données scalaire est spécial dans la mesure où il peut prendre n’importe quelle valeur d’autres types de données scalaires de la liste ci-dessous, ainsi que des tableaux et des conteneurs de propriétés. Plus précisément, une `dynamic` valeur peut être :

* Nul.
* Valeur de l’un des types de données scalaires primitifs : `bool` , `datetime` ,,,, `guid` `int` `long` `real` , `string` et `timespan` .
* Tableau de `dynamic` valeurs contenant zéro ou plusieurs valeurs avec une indexation de base zéro.
* Jeu de propriétés qui mappe des `string` valeurs uniques à des `dynamic` valeurs.
  Le conteneur de propriétés contient zéro, un ou plusieurs de ces mappages (appelés « emplacements »), indexés par les `string` valeurs uniques. Les emplacements ne sont pas ordonnés.

> [!NOTE]
> * Les valeurs de type `dynamic` sont limitées à 1 Mo (2 ^ 20).
> * Bien que le `dynamic` type apparaisse comme JSON, il peut contenir des valeurs que le modèle JSON ne représente pas, car il n’existe pas dans JSON (par exemple,,,, `long` `real` `datetime` `timespan` et `guid` ).
>   Par conséquent, dans la sérialisation des `dynamic` valeurs dans une représentation JSON, les valeurs que JSON ne peut pas représenter sont sérialisées en `string` valeurs. À l’inverse, Kusto analysera les chaînes en tant que valeurs fortement typées si elles peuvent être analysées en tant que telles.
>   Cela s’applique `datetime` aux `real` types,, `long` et `guid` . 
>   Pour plus d’informations sur le modèle objet JSON, consultez [JSON.org](https://json.org/).
> * Kusto ne tente pas de conserver l’ordre des mappages de nom à valeur dans un jeu de propriétés, et vous ne pouvez donc pas supposer que la commande est conservée. Il est tout à fait possible pour deux conteneurs de propriétés avec le même jeu de mappages de produire des résultats différents lorsqu’ils sont représentés en tant que `string` valeurs, par exemple.

## <a name="dynamic-literals"></a>Littéraux dynamiques

Un littéral de type se `dynamic` présente comme suit :

`dynamic(` *Valeur* `)`

La *valeur* peut être :

* `null`, auquel cas le littéral représente la valeur dynamique NULL : `dynamic(null)` .
* Autre littéral de type de données scalaire, auquel cas le littéral représente le `dynamic` littéral du type « Inner ». Par exemple, `dynamic(4)` est une valeur dynamique contenant la valeur 4 du type de données scalaire long.
* Tableau de littéraux dynamiques ou autres : `[` *ListOfValues* `]` . Par exemple, `dynamic([1, 2, "hello"])` est un tableau dynamique de trois éléments, deux `long` valeurs et une `string` valeur.
* Un conteneur de propriétés : valeur de `{` *nom* `=` *Value* . `}` .. Par exemple, `dynamic({"a":1, "b":{"a":2}})` est un conteneur de propriétés avec deux emplacements, `a` , et `b` , avec le deuxième emplacement qui est un autre conteneur de propriétés.

```kusto
print o=dynamic({"a":123, "b":"hello", "c":[1,2,3], "d":{}})
| extend a=o.a, b=o.b, c=o.c, d=o.d
```

Pour des raisons pratiques, `dynamic` les littéraux qui apparaissent dans le texte de la requête peuvent également inclure d’autres littéraux Kusto avec les types : `datetime` , `timespan` ,, `real` `long` , `guid` , `bool` et `dynamic` .
Cette extension sur JSON n’est pas disponible lors de l’analyse de chaînes (par exemple, lors de l’utilisation de la `parse_json` fonction ou de la réception de données), mais elle vous permet d’effectuer les opérations suivantes :

```kusto
print d=dynamic({"a": datetime(1970-05-11)})
```

Pour analyser une `string` valeur qui suit les règles d’encodage JSON dans une `dynamic` valeur, utilisez la `parse_json` fonction. Par exemple :

* `parse_json('[43, 21, 65]')` : tableau de nombres
* `parse_json('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` -dictionnaire
* `parse_json('21')` : valeur unique de type dynamique qui contient un nombre
* `parse_json('"21"')` : valeur unique de type dynamique qui contient une chaîne
* `parse_json('{"a":123, "b":"hello", "c":[1,2,3], "d":{}}')` -donne la même valeur que `o` dans l’exemple ci-dessus.

> [!NOTE]
> Contrairement à JavaScript, JSON impose l’utilisation de caractères guillemets doubles ( `"` ) autour des chaînes et des noms de propriété de jeu de propriétés.
> Par conséquent, il est généralement plus facile de citer un littéral de chaîne encodé JSON à l’aide d’un caractère guillemet simple ( `'` ).
  
L’exemple suivant montre comment vous pouvez définir une table qui contient une `dynamic` colonne (et une colonne), puis l’ingérer `datetime` en un seul enregistrement. Il montre également comment vous pouvez encoder des chaînes JSON dans des fichiers CSV :

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
|2015-01-01 00:00:00.0000000 | {« EventType » : « Demo », « EventValue » : « double-quote Love ! »}|

## <a name="dynamic-object-accessors"></a>Accesseurs d’objets dynamiques

Pour mettre un dictionnaire en indice, utilisez la notation à points ( `dict.key` ) ou la notation des crochets ( `dict["key"]` ).
Lorsque l’indice est une constante de chaîne, les deux options sont équivalentes.

> [!NOTE] 
> Pour utiliser une expression comme indice, utilisez la notation entre crochets. Lorsque vous utilisez des expressions arithmétiques, l’expression à l’intérieur des crochets doit être placée entre parenthèses.

Dans les exemples ci-dessous `dict` et `arr` sont des colonnes de type dynamique :

|Expression                        | Type d’expression d’accesseur | Signification                                                                              | Commentaires                                      |
|----------------------------------|--------------------------|--------------------------------------------------------------------------------------|-----------------------------------------------|
|dict [col]                         | Nom de l’entité (colonne)     | Indice un dictionnaire à l’aide des valeurs de la colonne `col` en tant que clé              | La colonne doit être de type chaîne                 | 
|arr [index]                        | Index d’entité (colonne)    | Indices d’un tableau à l’aide des valeurs de la colonne `index` comme index              | La colonne doit être de type entier ou booléen     | 
|arr [-index]                       | Index d’entité (colonne)    | Récupère la valeur’index'-th à partir de la fin du tableau                             | La colonne doit être de type entier ou booléen     |
|arr [(-1)]                         | Index d’entité             | Récupère la dernière valeur dans le tableau                                                |                                               |
|arr [ToInt ((indexAsString)]         | Appel de fonction            | Effectue un cast des valeurs de column `indexAsString` en int et les utilise pour indicer un tableau |                                               |
|dict [['WHERE']]                   | Mot clé utilisé comme nom d’entité (colonne) | Indice un dictionnaire à l’aide des valeurs de `where` la colonne comme clé    | Les noms d’entité identiques à certains mots clés de langage de requête doivent être placés entre guillemets. | 
|dict. ['WHERE'] ou dict ['WHERE']   | Constante                 | Indice un dictionnaire à l’aide d’une `where` chaîne comme clé                              |                                               |

**Astuce sur les performances :** Préférer utiliser des indices constants lorsque cela est possible

L’accès à un sous-objet d’une `dynamic` valeur produit une autre `dynamic` valeur, même si le sous-objet a un type sous-jacent différent. Utilisez la `gettype` fonction pour découvrir le type sous-jacent réel de la valeur et l’une des fonctions CAST listées ci-dessous pour effectuer un cast vers le type réel.

## <a name="casting-dynamic-objects"></a>Cast d’objets dynamiques

> Après avoir effectué un script d’un objet dynamique, vous devez effectuer un cast de la valeur en un type simple.

|Expression | Valeur | Type|
|---|---|---|
| X | parse_json (' [100101102] ')| tableau|
|X [0]|parse_json (' 100 ')|dynamique|
|ToInt ((X [1])|101| int|
| O | parse_json (' {"a1" : 100, "a b c" : "2015-01-01"} ')| dictionnaire|
|Y. a1|parse_json (' 100 ')|dynamique|
|Y ["a b c"]| parse_json (« 2015-01-01 »)|dynamique|
|ToDate (Y ["a b c"])|DateTime (2015-01-01)| DATETIME|

Les fonctions de conversion sont les suivantes :

* `tolong()`
* `todouble()`
* `todatetime()`
* `totimespan()`
* `tostring()`
* `toguid()`
* `todynamic()`

## <a name="building-dynamic-objects"></a>Génération d’objets dynamiques

Plusieurs fonctions vous permettent de créer des `dynamic` objets :

* [Pack ()](../packfunction.md) crée un conteneur de propriétés à partir de paires nom/valeur.
* [pack_array ()](../packarrayfunction.md) crée un tableau à partir de paires nom/valeur.
* [Range ()](../rangefunction.md) crée un tableau avec une série arithmétique de nombres.
* [zip ()](../zipfunction.md) paires valeurs « parallèles » de deux tableaux en un seul tableau.
* [REPEAT ()](../repeatfunction.md) crée un tableau avec une valeur répétée.

En outre, il existe plusieurs fonctions d’agrégation qui créent des `dynamic` tableaux pour stocker des valeurs agrégées :

* [buildschema ()](../buildschema-aggfunction.md) renvoie le schéma d’agrégation de plusieurs `dynamic` valeurs.
* [make_bag ()](../make-bag-aggfunction.md) retourne un jeu de propriétés de valeurs dynamiques dans le groupe.
* [make_bag_if ()](../make-bag-if-aggfunction.md) retourne un jeu de propriétés de valeurs dynamiques dans le groupe (avec un prédicat).
* [make_list ()](../makelist-aggfunction.md) retourne un tableau contenant toutes les valeurs, dans l’ordre.
* [make_list_if ()](../makelistif-aggfunction.md) retourne un tableau contenant toutes les valeurs, en séquence (avec un prédicat).
* [make_list_with_nulls ()](../make-list-with-nulls-aggfunction.md) retourne un tableau contenant toutes les valeurs, en séquence, y compris les valeurs NULL.
* [make_set ()](../makeset-aggfunction.md) retourne un tableau contenant toutes les valeurs uniques.
* [make_set_if ()](../makesetif-aggfunction.md) retourne un tableau contenant toutes les valeurs uniques (avec un prédicat).

## <a name="operators-and-functions-over-dynamic-types"></a>Opérateurs et fonctions sur des types dynamiques

|Opérateur ou fonction|Utilisation avec des types de données dynamiques|
|---|---|
| *value* `in` *array*| True s’il existe un élément de *array* qui est égal à *value*<br/>`where City in ('London', 'Paris', 'Rome')`
| *value* `!in` *array*| True s’il n’existe aucun élément de *array* qui est égal à *value*
|[`array_length(`ensemble`)`](../arraylengthfunction.md)| Null si ce n’est pas un tableau
|[`bag_keys(`conteneur propriétés`)`](../bagkeysfunction.md)| Énumère toutes les clés racines dans un objet de jeu de propriétés dynamique.
|[`bag_merge(`BAG1,..., bagN`)`](../bag-merge-function.md)| Fusionne des conteneurs de propriétés dynamiques dans un conteneur de propriétés dynamique avec toutes les propriétés fusionnées.
|[`extractjson(`chemin d’accès, objet`)`](../extractjsonfunction.md)|Utilise le chemin pour accéder à l’objet.
|[`parse_json(`code`)`](../parsejsonfunction.md)| Convertit une chaîne JSON en un objet dynamique.
|[`range(`de, à, étape`)`](../rangefunction.md)| Tableau de valeurs
|[`mv-expand` listColumn](../mvexpandoperator.md) | Réplique une ligne pour chaque valeur d’une liste dans une cellule spécifiée.
|[`summarize buildschema(`chronique`)`](../buildschema-aggfunction.md) |Déduit le schéma du type à partir du contenu de la colonne
|[`summarize make_bag(`chronique`)`](../make-bag-aggfunction.md) | Fusionne les valeurs du conteneur de propriétés (dictionnaire) de la colonne dans un jeu de propriétés, sans duplication de clé.
|[`summarize make_bag_if(`colonne, prédicat`)`](../make-bag-if-aggfunction.md) | Fusionne les valeurs du conteneur de propriétés (dictionnaire) de la colonne en un seul conteneur de propriétés, sans duplication de clé (avec prédicat).
|[`summarize make_list(`colonne `)`](../makelist-aggfunction.md)| Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau.
|[`summarize make_list_if(`colonne, prédicat `)`](../makelistif-aggfunction.md)| Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau (avec un prédicat).
|[`summarize make_list_with_nulls(`colonne `)`](../make-list-with-nulls-aggfunction.md)| Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau, y compris les valeurs NULL.
|[`summarize make_set(`chronique`)`](../makeset-aggfunction.md) | Aplatit des groupes de lignes et place les valeurs de la colonne dans un tableau, sans duplication.
