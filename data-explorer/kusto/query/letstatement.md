---
title: Laissez-nous faire la déclaration - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Let statement in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 890a6e21400048031e4ebd3df9749b6c47803e71
ms.sourcegitcommit: 653bfb3edf32553c52ef36b339c8b80713a601b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81524444"
---
# <a name="let-statement"></a>Let, instruction

Laissez les déclarations lier les noms aux expressions. Pour le reste de la portée dans laquelle l’instruction de laisser apparaît (portée globale ou dans une portée du corps de fonction), le nom peut être utilisé pour se référer à sa valeur liée. Si ce nom était auparavant lié à une autre valeur, la fixation de la déclaration la plus « la plus intérieure » est utilisée.

Laissez les déclarations améliorer la modularité et la réutilisation, car elles permettent de briser une expression potentiellement complexe en plusieurs parties, chacune liée à un nom par la déclaration de laisser, et de les composer ensemble. Ils peuvent également être utilisés pour créer des fonctions et des vues définies par l’utilisateur (expressions sur des tables dont les résultats ressemblent à une nouvelle table).

Les noms liés par les relevés de la loi doivent être des noms d’entités valides.

Les expressions liées par les déclarations de laisser peuvent être :
* De type scalar
* Type tabulaire
* Fonctions définies par l’utilisateur (lambdas)

**Syntaxe**

`let`*Nom* `=` *ScalarExpression* | *TabularExpression* | *FunctionDefinitionExpressionExpression*

* *Nom*: Le nom à lier. Le nom doit être un nom d’entité valide, `["Name with spaces"]`et le nom de l’entité s’échappant (p. ex., ) est autorisé. 
* *ScalarExpression*: Une expression avec un résultat scalaire dont la valeur sera liée au nom. Par exemple : `let one=1;`.
* *TabularExpression*: Une expression avec un résultat tabulaire dont la valeur sera liée au nom. Par exemple : `Logs | where Timestamp > ago(1h)`.
* *FonctionDéfinitionExpression*: Une expression qui donne un lambda (une déclaration de fonction anonyme) qui doit être lié au nom.
  Par exemple : `let f=(a:int, b:string) { strcat(b, ":", a) }`.

Les expressions Lambda ont la syntaxe suivante :

[`view` `(`] [*TabularArguments*`,`][ ][*ScalarArguments*]`)` `{` *FunctionBody*`}`

`TabularArguments`- [*TabularArgName* `:` `(`[*AtrName* `:` *AtrType*] [`,` ... ] `)`] [`,` ... ] [`,`]

 ou: - [*TabularArgName* `:` `(` `*` `)`]

`ScalarArguments`- [*ArgName* `:` *ArgType*] [`,` ... ]

* `view`peut apparaître seulement dans un lambda sans paramètres (celui qui n’a pas d’arguments) et indique que `union *`le nom lié sera inclus lorsque "toutes les tables" sont des requêtes (par exemple, lors de l’utilisation ).
* *TabularArguments* sont la liste des arguments officiels d’expression tabulaire.
  Chaque argument a :
  * *TabularArgName* - le nom de l’argument tabulaire formel. Le nom peut alors apparaître dans le *FunctionBody* et est lié à une valeur particulière lorsque le lambda est invoqué. 
  * Définition du schéma de table - une liste d’attributs avec leurs types (AtrName : AtrType).
  L’expression tabulaire qui est utilisée dans l’invocation lambda doit avoir tous ces attributs avec les types correspondants, mais ne se limite pas à eux. 
  ')' peut être utilisé comme expression tabulaire. Dans ce cas, toute expression tabulaire peut être utilisée dans l’invocation lambda et aucune de ses colonnes ne peut être consultée dans l’expression lambda.
  Tous les arguments tabulaires doivent apparaître devant les arguments scalar.
* *ScalarArguments* sont la liste des arguments formels scalar. 
  Chaque argument a :
  * *ArgName* - le nom de l’argument formel scalar. Le nom peut alors apparaître dans le *FunctionBody* et est lié à une valeur particulière lorsque le lambda est invoqué.  
  * *ArgType* - le type de l’argument scalaire formel. Actuellement, seuls les types suivants sont pris `bool` `string`en `long` `datetime`charge `timespan` `real`comme `dynamic` un type d’argument lambda: , , , , , et (et alias à ces types).

**Déclarations de laisser multiples et imbriqués**

Les déclarations de `;` laisser multiples peuvent être utilisées avec délimitant entre eux comme indiqué dans l’exemple suivant.
La dernière déclaration doit être une expression de requête valide : 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

Les déclarations de laisser imbriquées sont autorisées et peuvent être utilisées à l’intérieur d’une expression lambda.
Laissez les déclarations et les arguments visibles dans la portée actuelle et intérieure du corps de la fonction.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

**Exemples**

### <a name="using-let-to-define-constants"></a>Utilisation de laisser définir les constantes

L’exemple suivant lie `x` le nom à `1`la littéral scalaire, puis l’utilise dans une déclaration d’expression tabulaire:

```kusto
let x = 1;
range y from x to x step x
```

Même exemple, mais dans ce cas - le `['name']` nom de la déclaration de laisser est donné en utilisant la notion:

```kusto
let ['x'] = 1;
range y from x to x step x
```

Encore un autre exemple qui utilise laisser pour les valeurs scalar:

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="using-multiple-let-statements"></a>Utilisation de plusieurs déclarations de laisser

L’exemple suivant définit deux déclarations`foo2`de laisser`foo1`où une déclaration ( ) utilise une autre ( ).

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>Utilisation de la fonction de matérialisation

[`materialize`](materializefunction.md)la fonction permet de mettre en cache les résultats de sous-requête pendant le temps de l’exécution de la requête. 

```kusto
let totalPagesPerDay = PageViews
| summarize by Page, Day = startofday(Timestamp)
| summarize count() by Day;
let materializedScope = PageViews
| summarize by Page, Day = startofday(Timestamp);
let cachedResult = materialize(materializedScope);
cachedResult
| project Page, Day1 = Day
| join kind = inner
(
    cachedResult
    | project Page, Day2 = Day
)
on Page
| where Day2 > Day1
| summarize count() by Day1, Day2
| join kind = inner
    totalPagesPerDay
on $left.Day1 == $right.Day
| project Day1, Day2, Percentage = count_*100.0/count_1


```

|Jour1|Jour2|Pourcentage|
|---|---|---|
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34.0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16.618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14.6291376489636|
