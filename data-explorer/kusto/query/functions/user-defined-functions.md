---
title: Fonctions définies par l’utilisateur - Azure Data Explorer
description: Cet article décrit les fonctions définies par l’utilisateur (scalaires ou de vues) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.localizationpriority: high
ms.openlocfilehash: c85fd5dc784747314fe843d8f2325db76ee18d2c
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554667"
---
# <a name="user-defined-functions"></a>Fonctions définies par l’utilisateur

Les **fonctions définies par l'utilisateur** sont des sous-requêtes réutilisables qui peuvent être définies dans le cadre de la requête elle-même (**fonctions ad hoc**) ou être conservées dans le cadre des métadonnées de la base de données (**fonctions stockées**). Les fonctions définies par l'utilisateur sont appelées via un **nom**, sont fournies avec zéro ou plusieurs **arguments d'entrée** (qui peuvent être scalaires ou tabulaires) et produisent une valeur unique (qui peut être scalaire ou tabulaire) basée sur le **corps** de la fonction.

Une fonction définie par l'utilisateur appartient à l'une des deux catégories suivantes :

* Fonctions scalaires 
* Fonctions tabulaires, également appelées vues

Les arguments d'entrée et de sortie de la fonction déterminent si elle est scalaire ou tabulaire, ce qui indique ensuite comment elle peut être utilisée. 

## <a name="scalar-function"></a>Fonction scalaire

* Ne comporte aucun argument d'entrée, ou tous ses arguments d'entrée sont des valeurs scalaires
* Produit une valeur scalaire unique
* Peut être utilisée partout où une expression scalaire est autorisée
* Peut uniquement utiliser le contexte de ligne dans lequel elle est définie
* Peut uniquement faire référence à des tables (et vues) situées dans le schéma accessible

## <a name="tabular-function"></a>Fonction tabulaire

* Accepte un ou plusieurs arguments d'entrée tabulaires, et zéro ou plusieurs arguments d'entrée scalaires, et/ou :
* Produit une valeur tabulaire unique

## <a name="function-names"></a>Noms des fonctions

Les noms des fonctions définies par l'utilisateur valides doivent suivre les [règles de nommage des identificateurs](../schema-entities/entity-names.md#identifier-naming-rules) des autres entités.

Le nom doit également être unique dans son étendue de définition.

> [!NOTE]
> Si une fonction stockée et une table portent le même nom, la fonction stockée l'emporte lorsque le nom de la table/fonction est demandé.

## <a name="input-arguments"></a>Arguments d’entrée

Les fonctions définies par l'utilisateur valides suivent les règles suivantes :

* Une fonction définie par l'utilisateur comporte une liste fortement typée de zéro ou plusieurs arguments d'entrée.
* Un argument d'entrée comporte un nom, un type et (pour les arguments scalaires) une [valeur par défaut](#default-values).
* Le nom d'un argument d'entrée est un identificateur.
* Le type d'un argument d'entrée correspond à l'un des types de données scalaires ou à un schéma tabulaire.

Du point de vue syntaxique, la liste des arguments d'entrée est une liste de définitions d'arguments séparées par des virgules et entre parenthèses. Chaque définition d'argument est spécifiée en tant que

```
ArgName:ArgType [= ArgDefaultValue]
```
 Pour les arguments tabulaires, *ArgType* utilise la même syntaxe que la définition de table (parenthèses et liste de paires nom/type de colonne), avec le support supplémentaire d'un `(*)` solitaire indiquant « tout schéma tabulaire ».

Par exemple :

|Syntaxe                        |Description de la liste des arguments d'entrée                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |Aucun argument|
|`(s:string)`                  |Argument scalaire unique appelé `s` prenant une valeur de type `string`|
|`(a:long, b:bool=true)`       |Deux arguments scalaires, le second ayant une valeur par défaut    |
|`(T1:(*), T2(r:real), b:bool)`|Trois arguments (deux arguments tabulaires et un argument scalaire)  |

> [!NOTE]
> Lorsque vous utilisez à la fois des arguments d'entrée tabulaires et des arguments d'entrée scalaires, placez tous les arguments d'entrée tabulaires avant les arguments d'entrée scalaires.

## <a name="examples"></a>Exemples

Fonction scalaire :

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

Fonction tabulaire qui ne prend aucun argument :

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

Fonction tabulaire qui prend à la fois une entrée tabulaire et une entrée scalaire :

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v
};
MyFilter((range x from 1 to 10 step 1), 9)
```

|x|
|---|
|9|
|10|

Fonction tabulaire qui utilise une entrée tabulaire sans qu'aucune colonne ne soit spécifiée.
Une table peut être transmise à une fonction, et aucune colonne de table ne peut être référencée à l'intérieur de la fonction.

```kusto
let MyDistinct = (T:(*)) {
  T | distinct *
};
MyDistinct((range x from 1 to 3 step 1))
```

|x|
|---|
|1|
|2|
|3|

## <a name="declaring-user-defined-functions"></a>Déclaration de fonctions définies par l'utilisateur

La déclaration d'une fonction définie par l'utilisateur fournit ce qui suit :

* **Nom** de la fonction
* **Schéma** de la fonction (paramètres qu'elle accepte, le cas échéant)
* **Corps** de la fonction

> [!Note]
> La surcharge des fonctions n'est pas prise en charge. Vous ne pouvez pas créer plusieurs fonctions avec le même nom et des schémas d'entrée différents.

> [!TIP]
> Les fonctions lambda n'ont pas de nom, et sont liées à un nom au moyen d'une [instruction let](../letstatement.md). Elles peuvent donc être considérées comme des fonctions stockées définies par l'utilisateur.
> Exemple : déclaration d'une fonction lambda qui accepte deux arguments (un argument `string` appelé `s` et un argument `long` appelé `i`). Elle renvoie le produit du premier (après l'avoir converti en nombre) et du second. La fonction lambda est liée au nom `f` :

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

Le **corps** de la fonction comprend ce qui suit :

* Une seule expression, qui fournit la valeur renvoyée de la fonction (valeur scalaire ou tabulaire).
* Un certain nombre (zéro ou plus) d'[instructions let](../letstatement.md), dont l'étendue est celle du corps de la fonction. Le cas échéant, les instructions let doivent précéder l'expression définissant la valeur renvoyée de la fonction.
* Un certain nombre (zéro ou plus) d'[instructions de déclaration des paramètres de requête](../queryparametersstatement.md), qui déclarent les paramètres de requête utilisés par la fonction. Le cas échéant, elles doivent précéder l'expression définissant la valeur renvoyée de la fonction.

> [!NOTE]
> D'autres types d'[instructions de requête](../statements.md) pris en charge au « niveau supérieur » de la requête ne sont pas pris en charge dans le corps d'une fonction.

### <a name="examples-of-user-defined-functions"></a>Exemples de fonctions définies par l'utilisateur 

**Fonction définie par l'utilisateur qui utilise une instruction let**

L'exemple suivant lie le nom `Test` à une fonction définie par l'utilisateur (lambda) qui utilise trois instructions let. La sortie est `70` :

```kusto
let Test1 = (id: int) {
  let Test2 = 10;
  let Test3 = 10 + Test2 + id;
  let Test4 = (arg: int) {
      let Test5 = 20;
      Test2 + Test3 + Test5 + arg
  };
  Test4(10)
};
range x from 1 to Test1(10) step 1
| count
```

**Fonction définie par l'utilisateur qui définit une valeur par défaut pour un paramètre**

L'exemple suivant illustre une fonction qui accepte trois arguments. Les deux derniers comportent une valeur par défaut et ne doivent pas nécessairement être présents sur le site d'appel.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>Appel d'une fonction définie par l'utilisateur

Une fonction définie par l'utilisateur qui ne prend aucun argument peut être appelée soit par son nom, soit par son nom et une liste d'arguments vide entre parenthèses.

Exemples :

```kusto
// Bind the identifier a to a user-defined function (lambda) that takes
// no arguments and returns a constant of type long:
let a=(){123};
// Invoke the function in two equivalent ways:
range x from 1 to 10 step 1
| extend y = x * a, z = x * a() 
```

```kusto
// Bind the identifier T to a user-defined function (lambda) that takes
// no arguments and returns a random two-by-two table:
let T=(){
  range x from 1 to 2 step 1
  | project x1 = rand(), x2 = rand()
};
// Invoke the function in two equivalent ways:
// (Note that the second invocation must be itself wrapped in
// an additional set of parentheses, as the union operator
// differentiates between "plain" names and expressions)
union T, (T())
```

Une fonction définie par l'utilisateur qui prend un ou plusieurs arguments scalaires peut être appelée en utilisant le nom de la table et une liste d'arguments concrets entre parenthèses :

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

Une fonction définie par l'utilisateur qui prend un ou plusieurs arguments de table (et un nombre quelconque d'arguments scalaires) peut être appelée en utilisant le nom de la table et une liste d'arguments concrets entre parenthèses :

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

Vous pouvez également utiliser l'opérateur `invoke` pour appeler une fonction définie par l'utilisateur qui prend un ou plusieurs arguments de table et renvoie une table. Cette fonction est utile lorsque le premier argument de table concret de la fonction est la source de l'opérateur `invoke` :

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>Valeurs par défaut

Les fonctions peuvent fournir des valeurs par défaut à certains de leurs paramètres dans les conditions suivantes :

* Des valeurs par défaut ne peuvent être fournies que pour les paramètres scalaires.
* Les valeurs par défaut sont toujours des littéraux (constantes). Il ne peut pas s'agir de calculs arbitraires.
* Les paramètres sans valeur par défaut précèdent toujours les paramètres qui en ont une.
* Les appelants doivent fournir la valeur de tous les paramètres sans valeur par défaut, dans le même ordre que dans la déclaration de fonction.
* Les appelants ne sont pas tenus de fournir la valeur des paramètres dotés d'une valeur par défaut, mais ils peuvent le faire.
* Les appelants peuvent fournir des arguments dans un ordre qui ne correspond pas à celui des paramètres. Dans ce cas, ils doivent nommer leurs arguments.

L'exemple suivant renvoie une table comportant deux enregistrements identiques. Lors du premier appel de `f`, les arguments sont complètement « brouillés », de sorte que chacun reçoit explicitement un nom :

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
union
  (print x=f(c=7, a=12)), // "12-b.default-7"
  (print x=f(12, c=7))    // "12-b.default-7"
```

|x|
|---|
|12-b.default-7|
|12-b.default-7|

## <a name="view-functions"></a>Fonctions View

Une fonction définie par l'utilisateur qui ne prend aucun argument et renvoie une expression tabulaire peut être marquée comme **view**. Cela signifie que la fonction se comporte comme une table chaque fois que la résolution de noms de la table des caractères génériques est exécutée.
L'exemple suivant illustre deux fonctions définies par l'utilisateur, `T_view` et `T_notview`, et montre que seule la première est résolue par le caractère générique dans `union` :

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>Restrictions

Les restrictions suivantes s’appliquent :

* Les fonctions définies par l'utilisateur ne peuvent pas être transmises dans les informations d'appel [toscalar ()](../toscalarfunction.md) qui dépendent du contexte de ligne dans lequel la fonction est appelée.
* Les fonctions définies par l'utilisateur qui renvoient une expression tabulaire ne peuvent pas être appelées avec un argument qui varie en fonction du contexte de ligne.
* Une fonction prenant au moins une entrée tabulaire ne peut pas être appelée sur un cluster distant.
* Une fonction scalaire ne peut pas être appelée sur un cluster distant.

Une fonction définie par l'utilisateur ne peut être appelée avec un argument qui varie selon le contexte de ligne que lorsqu'elle est uniquement composée de fonctions scalaires et n'utilise pas `toscalar()`.

**Exemple de restriction 1**

```kusto
// Supported:
// f is a scalar function that doesn't reference any tabular expression
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { now() + hours*1h };
Table2 | where Column != 123 | project d = f(10)

// Supported:
// f is a scalar function that references the tabular expression Table1,
// but is invoked with no reference to the current row context f(10):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(10)

// Not supported:
// f is a scalar function that references the tabular expression Table1,
// and is invoked with a reference to the current row context f(Column):
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { toscalar(Table1 | summarize min(xdate) - hours*1h) };
Table2 | where Column != 123 | project d = f(Column)
```

**Exemple de restriction 2**

```kusto
// Not supported:
// f is a tabular function that is invoked in a context
// that expects a scalar value.
let Table1 = datatable(xdate:datetime)[datetime(1970-01-01)];
let Table2 = datatable(Column:long)[1235];
let f = (hours:long) { range x from 1 to hours step 1 | summarize make_list(x) };
Table2 | where Column != 123 | project d = f(Column)
```

## <a name="features-that-are-currently-unsupported-by-user-defined-functions"></a>Fonctionnalités actuellement non prises en charge par les fonctions définies par l'utilisateur

Pour être complet, voici quelques fonctionnalités couramment demandées pour les fonctions définies par l'utilisateur mais qui ne sont actuellement pas prises en charge :

1.  Surcharge de fonction : il est actuellement impossible de surcharger une fonction (c'est-à-dire de créer plusieurs fonctions avec le même nom et un schéma d'entrée différent).

2.  Valeurs par défaut : la valeur par défaut d'un paramètre scalaire relatif à une fonction doit être un littéral scalaire (constante). De plus, les fonctions stockées ne peuvent pas comporter de valeur par défaut de type `dynamic`.
