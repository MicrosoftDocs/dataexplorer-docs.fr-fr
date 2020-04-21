---
title: Fonctions définies par l’utilisateur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les fonctions définies par l’utilisateur dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: e8372be303b1540e1421f226ed0fd0fe74d44545
ms.sourcegitcommit: c4aea69fafa9d9fbb814764eebbb0ae93fa87897
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81610214"
---
# <a name="user-defined-functions"></a>Fonctions définies par l'utilisateur

Kusto prend en charge les fonctions définies par l’utilisateur, qui sont soit :
* Une partie de la requête**elle-même (fonctions ad hoc**) 
* Stockés de manière persistante dans le cadre des métadonnées de base de données **(fonctions stockées**)

Une fonction définie par l’utilisateur a :
* Un nom:
    * Doit suivre les [règles de nommage de l’identifiant](../schema-entities/entity-names.md#identifier-naming-rules)
    * Doit être unique dans la portée de la définition avec une spécification de type
* Une liste fortement typée de paramètres d’entrée :
    * Peut être des expressions scalaires ou tabulaires
    * Les paramètres scalaires peuvent être fournis avec une valeur par défaut, utilisé implicitement lorsque l’appelant de la fonction ne fournit pas une valeur pour le paramètre (voir [valeurs par défaut](#default-values), ci-dessous)
* Une valeur de rendement fortement typée, qui peut être scalaire ou tabulaire

Les entrées et la sortie d’une fonction déterminent comment et où elle peut être utilisée :

* **Une fonction scalaire**: 
    * Est une fonction sans entrées, ou avec des entrées scalaires seulement, et qui produit une sortie scalaire
    * Peut être utilisé partout où une expression scalaire est autorisée
    * Ne peut utiliser que le contexte de la ligne dans lequel il est défini
    * Ne peut se référer qu’aux tables (et vues) qui sont dans le schéma accessible

Exemple :

```kusto
let Add7 = (arg0:long = 5) { arg0 + 7 };
range x from 1 to 10 step 1
| extend x_plus_7 = Add7(x), five_plus_seven = Add7()
```

* **Une fonction tabulaire**: 
    * Est une fonction sans entrées, ou au moins une entrée tabulaire, et produit une sortie tabulaire
    * Peut être utilisé partout où une expression tabulaire est autorisée 

> [!NOTE]
> Tous les paramètres tabulaires doivent apparaître avant les paramètres scalaires.

Exemple d’une fonction tabulaire qui ne prend aucun argument :

```kusto
let tenNumbers = () { range x from 1 to 10 step 1};
tenNumbers
| extend x_plus_7 = x + 7
```

Exemple d’une fonction tabulaire qui utilise une entrée tabulaire et une entrée scalaire :

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

Exemple d’une fonction tabulaire qui utilise une entrée tabulaire sans colonne spécifiée. N’importe quelle table peut être transmise à une fonction, et aucune colonne de table ne peut être référencée à l’intérieur de la fonction.

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

## <a name="declaring-user-defined-functions"></a>Déclarer les fonctions définies par l’utilisateur

La déclaration d’une fonction définie par l’utilisateur fournit :

* **Nom** de fonction
* Schéma **de fonction** (paramètres qu’il accepte, le cas échéant)
* **Corps de fonction**

> [!Note]
> Les fonctions de surcharge ne sont pas prises en charge. Par exemple, vous ne pouvez pas créer plusieurs fonctions avec le même nom et différents schémas d’entrée.

> [!TIP]
> Fonctions Lambda n’ont pas de nom et sont liés à un nom en utilisant une [déclaration de laisser](../letstatement.md). Par conséquent, ils peuvent être considérés comme des fonctions stockées définies par l’utilisateur.
> Exemple : Déclaration pour une fonction lambda `string` qui `s` accepte `long` `i`deux arguments (un appelé et un appelé ). Il retourne le produit du premier (après l’avoir converti en un nombre) et le second. Le lambda est lié `f`au nom :

```kusto
let f=(s:string, i:long) {
    tolong(s) * i
};
```

Le **corps de** fonction comprend :

* Exactement une expression, qui fournit la valeur de retour de la fonction (valeur scalaire ou tabulaire).
* N’importe quel nombre (zéro ou plus) des [déclarations de laisser](../letstatement.md), dont la portée est celle du corps de fonction. S’ils sont spécifiés, les déclarations de laisser doivent précéder l’expression définissant la valeur de retour de la fonction.
* N’importe quel nombre (zéro ou plus) des énoncés de paramètres de [requête,](../queryparametersstatement.md)qui déclarent les paramètres de requête utilisés par la fonction. S’ils sont spécifiés, ils doivent précéder l’expression définissant la valeur de retour de la fonction.

> [!NOTE]
> D’autres types [d’énoncés](../statements.md) de requête qui sont pris en charge à la requête "niveau supérieur", ne sont pas pris en charge à l’intérieur d’un corps de fonction.

### <a name="examples-of-user-defined-functions"></a>Exemples de fonctions définies par l’utilisateur 

**Fonction définie par l’utilisateur qui utilise une déclaration de laisser**

L’exemple suivant lie `Test` le nom à une fonction définie par l’utilisateur (lambda) qui utilise trois instructions de laisser. La sortie `70`est :

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

L’exemple suivant montre une fonction qui accepte trois arguments. Ces deux derniers ont une valeur par défaut et n’ont pas à être présents sur le site d’appel.

```kusto
let f = (a:long, b:string = "b.default", c:long = 0) {
  strcat(a, "-", b, "-", c)
};
print f(12, c=7) // Returns "12-b.default-7"
```

## <a name="invoking-a-user-defined-function"></a>Invoquer une fonction définie par l’utilisateur

Une fonction définie par l’utilisateur qui ne prend aucun argument peut être invoquée par son nom ou par son nom avec une liste d’arguments vide entre parenthèses.

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

Une fonction définie par l’utilisateur qui prend un ou plusieurs arguments scalaires peut être invoquée en utilisant le nom de table et une liste d’arguments concrets entre parenthèses :

```kusto
let f=(a:string, b:string) {
  strcat(a, " (la la la)", b)
};
print f("hello", "world")
```

Une fonction définie par l’utilisateur qui prend un ou plusieurs arguments de table (et n’importe quel nombre d’arguments scalaires) peut être invoquée à l’aide du nom de table et d’une liste d’arguments concrets entre parenthèses :

```kusto
let MyFilter = (T:(x:long), v:long) {
  T | where x >= v 
};
MyFilter((range x from 1 to 10 step 1), 9)
```

Vous pouvez également `invoke` utiliser l’opérateur pour invoquer une fonction définie par l’utilisateur qui prend un ou plusieurs arguments de table et renvoie une table. Ceci est utile lorsque le premier argument concret de `invoke` la table à la fonction est la source de l’opérateur:

```kusto
let append_to_column_a=(T:(a:string), what:string) {
    T | extend a=strcat(a, " ", what)
};
datatable (a:string) ["sad", "really", "sad"]
| invoke append_to_column_a(":-)")
```

## <a name="default-values"></a>Valeurs par défaut

Les fonctions peuvent fournir des valeurs par défaut à certains de leurs paramètres dans les conditions suivantes :

* Les valeurs par défaut peuvent être fournies uniquement pour les paramètres scalaires.
* Les valeurs par défaut sont toujours littérales (constantes). Ce ne sont pas des calculs arbitraires.
* Les paramètres sans valeur par défaut précèdent toujours les paramètres qui ont une valeur par défaut.
* Les appelants doivent fournir la valeur de tous les paramètres sans valeurs par défaut disposées dans le même ordre que la déclaration de fonction.
* Les appelants n’ont pas besoin de fournir la valeur pour les paramètres avec des valeurs par défaut, mais peuvent le faire.
* Les appelants peuvent fournir des arguments dans un ordre qui ne correspond pas à l’ordre des paramètres. Si c’est le cas, ils doivent nommer leurs arguments.

L’exemple suivant renvoie un tableau avec deux enregistrements identiques. Dans la première invocation de `f`, les arguments sont complètement "brouillés", de sorte que chacun est explicitement donné un nom:

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

## <a name="view-functions"></a>Afficher les fonctions

Une fonction définie par l’utilisateur qui ne prend aucun argument et renvoie une expression tabulaire peut être marquée comme une **vue**. Le marquage d’une fonction définie par l’utilisateur comme une vue signifie que la fonction se comporte comme une table chaque fois que la résolution du nom de table wildcard est faite.
L’exemple suivant montre deux fonctions définies par l’utilisateur, `T_view` et, `T_notview`et montre `union`comment seule la première est résolue par la référence wildcard dans le :

```kusto
let T_view = view () { print x=1 };
let T_notview = () { print x=2 };
union T*
```

## <a name="user-defined-functions-usage-restrictions"></a>Restrictions d’utilisation des fonctions définies par l’utilisateur

Les restrictions suivantes s’appliquent :

* Les fonctions définies par l’utilisateur ne peuvent pas passer dans les informations d’invocation [toscalar()](../toscalarfunction.md) qui dépendent du contexte de ligne dans lequel la fonction est appelée.
* Les fonctions définies par l’utilisateur qui renvoient une expression tabulaire ne peuvent pas être invoquées avec un argument qui varie selon le contexte de la ligne.
* Une fonction prenant au moins une entrée tabulaire ne peut pas être invoquée sur un cluster distant.
* Une fonction scalaire ne peut pas être invoquée sur un cluster distant.

Le seul endroit où une fonction définie par l’utilisateur peut être invoquée avec un argument qui varie selon le `toscalar()`contexte de la ligne est lorsque la fonction définie par l’utilisateur est composée de fonctions scalaires seulement et n’utilise pas .

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
