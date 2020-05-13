---
title: Instruction Let-Azure Explorateur de données
description: Cet article décrit l’instruction Let dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c2e21f0b41f34b469e409109a2586f3e5fd98fa5
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271466"
---
# <a name="let-statement"></a>Let, instruction

Les instructions Let lient des noms à des expressions. Pour le reste de l’étendue dans laquelle l’instruction Let apparaît (portée globale ou dans une portée de corps de fonction), le nom peut être utilisé pour faire référence à sa valeur liée. Si ce nom a déjà été lié à une autre valeur, la liaison d’instruction Let « la plus profonde » est utilisée.

Les instructions Let améliorent la modularité et la réutilisation, car elles permettent de fractionner une expression potentiellement complexe en plusieurs parties, chacune liée à un nom par l’intermédiaire de l’instruction Let, et de les composer ensemble. Elles peuvent également être utilisées pour créer des fonctions et des vues définies par l’utilisateur (expressions sur des tables dont les résultats ressemblent à une nouvelle table).

Les noms liés par les instructions Let doivent être des noms d’entité valides.

Les expressions liées par les instructions Let peuvent être :
* De type scalaire
* De type tabulaire
* Fonctions définies par l’utilisateur (lambdas)

**Syntaxe**

`let`*Nom* `=` *ScalarExpression*  |  *TabularExpression*  |  *FunctionDefinitionExpression*

* *Nom*: nom à lier. Le nom doit être un nom d’entité valide et le nom d’entité échappement (par exemple, `["Name with spaces"]` ) est autorisé. 
* *ScalarExpression*: expression avec un résultat scalaire dont la valeur sera liée au nom. Par exemple : `let one=1;`.
* *TabularExpression*: expression avec un résultat tabulaire dont la valeur sera liée au nom. Par exemple : `Logs | where Timestamp > ago(1h)`.
* *FunctionDefinitionExpression*: expression qui produit une expression lambda (une déclaration de fonction anonyme) qui doit être liée au nom.
  Par exemple : `let f=(a:int, b:string) { strcat(b, ":", a) }`.

Les expressions lambda ont la syntaxe suivante :

[ `view` ] `(` [*TabularArguments*] [ `,` ] [*ScalarArguments*] `)` `{` *FunctionBody*`}`

`TabularArguments`-[*TabularArgName* `:` `(` [*AtrName* `:` *AtrType*] [ `,` ...] `)` ] [`,` ... ] [`,`]

 ou :-[*TabularArgName* `:` `(` `*` `)` ]

`ScalarArguments`-[*ArgName* `:` *ArgType*] [ `,` ...]

* `view`peut apparaître uniquement dans une expression lambda sans paramètre (qui n’a pas d’argument) et indique que le nom lié sera inclus quand « toutes les tables » sont des requêtes (par exemple, lors de l’utilisation de `union *` ).
* *TabularArguments* est la liste des arguments d’expression tabulaires formels.
  Chaque argument a :
  * *TabularArgName* : nom de l’argument tabulaire formel. Le nom peut alors apparaître dans le *FunctionBody* et être lié à une valeur particulière lorsque l’expression lambda est appelée. 
  * Définition de schéma de table : liste d’attributs avec leurs types (AtrName : AtrType).
  L’expression tabulaire utilisée dans l’appel lambda doit avoir tous ces attributs avec les types correspondants, mais ne leur est pas limité. 
  ' (*) 'peut être utilisé en tant qu’expression tabulaire. Dans ce cas, toute expression tabulaire peut être utilisée dans l’appel lambda et aucune de ses colonnes n’est accessible dans l’expression lambda.
  Tous les arguments tabulaires doivent apparaître avant les arguments scalaires.
* *ScalarArguments* est la liste des arguments scalaires formels. 
  Chaque argument a :
  * *ArgName* : nom de l’argument scalaire formel. Le nom peut alors apparaître dans le *FunctionBody* et être lié à une valeur particulière lorsque l’expression lambda est appelée.  
  * *ArgType* : type de l’argument scalaire formel. Actuellement, seuls les types suivants sont pris en charge en tant que type d’argument lambda : `bool` ,,,, `string` `long` `datetime` `timespan` , `real` et `dynamic` (et les alias de ces types).

**Instructions Let multiples et imbriquées**

Plusieurs instructions Let peuvent être utilisées avec des séparateurs `;` , comme indiqué dans l’exemple suivant.
La dernière instruction doit être une expression de requête valide : 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

Les instructions Let imbriquées sont autorisées et peuvent être utilisées dans une expression lambda.
Les instructions et les arguments Let sont visibles dans l’étendue actuelle et intérieure du corps de la fonction.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

**Exemples**

### <a name="using-let-to-define-constants"></a>Utilisation de Let pour définir des constantes

L’exemple suivant lie le nom `x` au littéral scalaire, puis l' `1` utilise dans une instruction d’expression tabulaire :

```kusto
let x = 1;
range y from x to x step x
```

Même exemple, mais dans ce cas, le nom de l’instruction Let est fourni à l’aide de la `['name']` notion :

```kusto
let ['x'] = 1;
range y from x to x step x
```

Un autre exemple qui utilise Let pour les valeurs scalaires :

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="using-multiple-let-statements"></a>Utilisation de plusieurs instructions Let

L’exemple suivant définit deux instructions Let où une instruction ( `foo2` ) utilise une autre ( `foo1` ).

```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="using-materialize-function"></a>Utilisation de la fonction matérialiser

[`materialize`](materializefunction.md)la fonction permet de mettre en cache les résultats de la sous-requête au moment de l’exécution de la requête. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
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
