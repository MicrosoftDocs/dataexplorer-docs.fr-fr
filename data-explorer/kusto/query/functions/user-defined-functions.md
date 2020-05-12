---
title: Fonctions définies par l’utilisateur-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les fonctions définies par l’utilisateur dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e9e199631d57f3fd3be8d438e6b0cdbf9bdd4266
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227357"
---
# <a name="user-defined-functions"></a>Fonctions définies par l’utilisateur

Les **fonctions définies par l’utilisateur** sont des sous-requêtes réutilisables qui peuvent être définies dans le cadre de la requête elle-même (**fonctions ad hoc**) ou conservées dans le cadre des métadonnées de la base de données (**fonctions stockées**). Les fonctions définies par l’utilisateur sont appelées à l’aide d’un **nom**, sont fournies avec zéro, un ou plusieurs **arguments d’entrée** (qui peuvent être scalaires ou tabulaires) et produisent une seule valeur (qui peut être scalaire ou tabulaire) en fonction du **corps**de la fonction.

Une fonction définie par l’utilisateur appartient à l’une des deux catégories suivantes :

* Fonctions scalaires 
* Fonctions tabulaires 

Les arguments d’entrée et la sortie de la fonction déterminent s’il s’agit d’une valeur scalaire ou tabulaire, qui établit ensuite la manière dont il peut être utilisé. 

## <a name="scalar-function"></a>Fonction scalaire

* N’a aucun argument d’entrée, ou tous ses arguments d’entrée sont des valeurs scalaires
* Produit une valeur scalaire unique
* Peut être utilisé partout où une expression scalaire est autorisée
* Peut uniquement utiliser le contexte de ligne dans lequel il est défini
* Peut uniquement faire référence à des tables (et des vues) qui se trouvent dans le schéma accessible

## <a name="tabular-function"></a>Fonction tabulaire

* Accepte un ou plusieurs arguments d’entrée tabulaires et zéro, un ou plusieurs arguments d’entrée scalaires, et/ou :
* Produit une valeur tabulaire unique

## <a name="function-names"></a>Noms de fonction

Les noms de fonctions définies par l’utilisateur valides doivent respecter les mêmes [règles d’affectation](../schema-entities/entity-names.md#identifier-naming-rules) de noms que les autres entités.

Le nom doit également être unique dans son étendue de définition.

> [!NOTE]
> La surcharge de fonction n’est pas prise en charge. Vous ne pouvez pas définir plusieurs fonctions en utilisant le même nom.

## <a name="input-arguments"></a>Arguments d’entrée

Les fonctions définies par l’utilisateur valides suivent les règles suivantes :

* Une fonction définie par l’utilisateur a une liste fortement typée de zéro ou plusieurs arguments d’entrée.
* Un argument d’entrée a un nom, un type et (pour les arguments scalaires) une [valeur par défaut](#default-values).
* Le nom d’un argument d’entrée est un identificateur.
* Le type d’un argument d’entrée est l’un des types de données scalaires ou un schéma tabulaire.

Syntaxiquement, la liste d’arguments d’entrée est une liste séparée par des virgules de définitions d’arguments, incluse entre parenthèses. Chaque définition d’argument est spécifiée en tant que

```
ArgName:ArgType [= ArgDefaultValue]
```
 Pour les arguments tabulaires, *ArgType* a la même syntaxe que la définition de table (parenthèse et une liste de paires nom/type de colonne), avec la prise en charge supplémentaire d’un solitaires `(*)` indiquant « n’importe quel schéma tabulaire ».

Par exemple :

|Syntaxe                        |Description de la liste des arguments d’entrée                                 |
|------------------------------|-----------------------------------------------------------------|
|`()`                          |Aucun argument|
|`(s:string)`                  |Argument scalaire unique appelé `s` acceptant une valeur de type`string`|
|`(a:long, b:bool=true)`       |Deux arguments scalaires, le second ayant une valeur par défaut    |
|`(T1:(*), T2(r:real), b:bool)`|Trois arguments (deux arguments tabulaires et un argument scalaire)  |

> [!NOTE]
> Lorsque vous utilisez des arguments d’entrée tabulaires et des arguments d’entrée scalaires, placez tous les arguments d’entrée tabulaires avant les arguments d’entrée scalaires.

## <a name="examples"></a>Exemples

Fonction scalaire :

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

Fonction tabulaire ne acceptant aucun argument :

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

Une fonction tabulaire acceptant une entrée tabulaire et une entrée scalaire :

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

Fonction tabulaire qui utilise une entrée tabulaire sans colonne spécifiée.
Une table peut être transmise à une fonction, et aucune colonne de table ne peut être référencée à l’intérieur de la fonction.

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

## <a name="declaring-user-defined-functions"></a>Déclaration de fonctions définies par l’utilisateur

La déclaration d’une fonction définie par l’utilisateur fournit les éléments suivants :

* **Nom** de la fonction
* **Schéma** de fonction (paramètres qu’il accepte, le cas échéant)
* **Corps** de la fonction

> [!Note]
> La surcharge des fonctions n’est pas prise en charge. Vous ne pouvez pas créer plusieurs fonctions avec le même nom et des schémas d’entrée différents.

> [!TIP]
> Les fonctions lambda n’ont pas de nom et sont liées à un nom à l’aide d’une [instruction Let](../letstatement.md). Par conséquent, ils peuvent être considérés comme des fonctions stockées définies par l’utilisateur.
> Exemple : déclaration pour une fonction lambda qui accepte deux arguments (une `string` appelée `s` et une `long` appelée `i` ). Elle retourne le produit de la première (après l’avoir convertie en nombre) et la seconde. Le lambda est lié au nom `f` :

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

Le **corps** de la fonction comprend les éléments suivants :

* Exactement une expression, qui fournit la valeur de retour de la fonction (valeur scalaire ou tabulaire).
* Tout nombre (zéro ou plus) des [instructions Let](../letstatement.md), dont la portée est celle du corps de la fonction. S’il est spécifié, les instructions Let doivent précéder l’expression définissant la valeur de retour de la fonction.
* Tout nombre (zéro ou plus) d' [instructions de paramètres de requête](../queryparametersstatement.md), qui déclarent des paramètres de requête utilisés par la fonction. S’ils sont spécifiés, ils doivent précéder l’expression définissant la valeur de retour de la fonction.

> [!NOTE]
> D’autres types d' [instructions de requête](../statements.md) prises en charge au niveau supérieur de la requête ne sont pas pris en charge dans le corps d’une fonction.

### <a name="examples-of-user-defined-functions"></a>Exemples de fonctions définies par l’utilisateur 

**Fonction définie par l’utilisateur qui utilise une instruction Let**

L’exemple suivant lie le nom `Test` à une fonction définie par l’utilisateur (lambda) qui utilise trois instructions Let. La sortie est la `70` suivante :

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

**Fonction définie par l’utilisateur qui définit une valeur par défaut pour un paramètre**

L’exemple suivant montre une fonction qui accepte trois arguments. Les deux derniers ont une valeur par défaut et ne doivent pas être présents sur le site d’appel.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>Appel d’une fonction définie par l’utilisateur

Une fonction définie par l’utilisateur qui ne prend pas d’arguments peut être appelée par son nom ou par son nom et par une liste d’arguments vide entre parenthèses.

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

Une fonction définie par l’utilisateur qui accepte un ou plusieurs arguments scalaires peut être appelée à l’aide du nom de table et d’une liste d’arguments concrets entre parenthèses :

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

Une fonction définie par l’utilisateur qui accepte un ou plusieurs arguments de table (et un nombre quelconque d’arguments scalaires) peut être appelée à l’aide du nom de table et d’une liste d’arguments concrets entre parenthèses :

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

Vous pouvez également utiliser l’opérateur `invoke` pour appeler une fonction définie par l’utilisateur qui prend un ou plusieurs arguments de table et retourne une table. Cette fonction est utile lorsque le premier argument de table concret de la fonction est la source de l' `invoke` opérateur :

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>Valeurs par défaut

Les fonctions peuvent fournir des valeurs par défaut à certains de leurs paramètres dans les conditions suivantes :

* Les valeurs par défaut peuvent être fournies uniquement pour les paramètres scalaires.
* Les valeurs par défaut sont toujours des littéraux (constantes). Ils ne peuvent pas être des calculs arbitraires.
* Les paramètres sans valeur par défaut précèdent toujours les paramètres qui ont une valeur par défaut.
* Les appelants doivent fournir la valeur de tous les paramètres sans valeurs par défaut organisées dans le même ordre que la déclaration de la fonction.
* Les appelants n’ont pas besoin de fournir la valeur des paramètres avec des valeurs par défaut, mais cela peut le faire.
* Les appelants peuvent fournir des arguments dans un ordre qui ne correspond pas à l’ordre des paramètres. Dans ce cas, ils doivent nommer leurs arguments.

L’exemple suivant retourne une table avec deux enregistrements identiques. Dans le premier appel de `f` , les arguments sont complètement « brouillés », afin que chacun d’eux soit explicitement affecté d’un nom :

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
|12-b. par défaut-7|
|12-b. par défaut-7|

## <a name="view-functions"></a>Afficher les fonctions

Une fonction définie par l’utilisateur qui ne prend pas d’arguments et retourne une expression tabulaire peut être marquée en tant que **vue**. Le marquage d’une fonction définie par l’utilisateur en tant que vue signifie que la fonction se comporte comme une table chaque fois que la résolution de noms de table de caractères génériques est effectuée.
L’exemple suivant montre deux fonctions définies par l’utilisateur, `T_view` et `T_notview` , et montre comment seule la première est résolue par la référence de caractère générique dans le `union` :

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="restrictions"></a>Restrictions

Les restrictions suivantes s’appliquent :

* Les fonctions définies par l’utilisateur ne peuvent pas passer dans les informations d’appel [toscalar ()](../toscalarfunction.md) qui dépendent du contexte de ligne dans lequel la fonction est appelée.
* Fonctions définies par l’utilisateur qui retournent une expression tabulaire can’tbe appelée avec un argument qui varie selon le contexte de ligne.
* Une fonction acceptant au moins une entrée tabulaire ne peut pas être appelée sur un cluster distant.
* Une fonction scalaire ne peut pas être appelée sur un cluster distant.

La seule place qu’une fonction définie par l’utilisateur peut être appelée avec un argument qui varie avec le contexte de ligne est lorsque la fonction définie par l’utilisateur est composée uniquement de fonctions scalaires et n’utilise pas `toscalar()` .

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
