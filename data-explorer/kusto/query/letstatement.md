---
title: Instruction Let - Azure Data Explorer
description: Cet article décrit l’instruction Let dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.localizationpriority: high
ms.openlocfilehash: 7723a3ccd06963596744db9c73f499a882b54c3e
ms.sourcegitcommit: 1530a38181ec92ed1c2c1f3aa2a75f69bd3e9045
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822778"
---
# <a name="let-statement"></a>Let, instruction

Les instructions Let lient des noms à des expressions. Pour le reste de la portée dans laquelle l’instruction Let apparaît, le nom peut être utilisé pour faire référence à sa valeur liée. L’instruction Let peut figurer dans une portée globale ou une portée de corps de fonction.
Si ce nom a déjà été lié à une autre valeur, la liaison d’instruction Let « la plus intérieure » est utilisée.

Les instructions Let améliorent la modularité et la réutilisation, car elles vous permettent de décomposer une expression potentiellement complexe en plusieurs parties.
Chaque composant est lié à un nom via l’instruction Let, et ensemble, ils composent l’ensemble. Elles peuvent également être utilisées pour créer des fonctions et des vues définies par l’utilisateur. Les vues sont des expressions sur des tables dont les résultats ressemblent à une nouvelle table.

> [!NOTE]
> Les noms liés par les instructions Let doivent être des noms d’entité valides.

Les expressions liées par les instructions Let peuvent être :
* Types scalaires
* Types tabulaires
* Fonctions définies par l’utilisateur (lambdas)

## <a name="syntax"></a>Syntaxe

`let` *Name* `=` *ScalarExpression* | *TabularExpression* | *FunctionDefinitionExpression*

|Champ  |Définition  |Exemple  |
|---------|---------|---------|
|*Nom*   | Nom à lier. Le nom doit être un nom d’entité valide.    |L’échappement du nom d’entité, par exemple `["Name with spaces"]`, est autorisé.      |
|*ScalarExpression*     |  Expression avec un résultat scalaire dont la valeur est liée au nom.  | `let one=1;`  |
|*TabularExpression*    | Expression avec un résultat tabulaire dont la valeur est liée au nom.   | `Logs | where Timestamp > ago(1h)`    |
|*FunctionDefinitionExpression*   | Expression qui produit une expression lambda, une déclaration de fonction anonyme qui doit être liée au nom.   |  `let f=(a:int, b:string) { strcat(b, ":", a) }`  |


### <a name="lambda-expressions-syntax"></a>Syntaxe des expressions lambda

[`view`] `(`[*TabularArguments*][`,`][*ScalarArguments*]`)` `{` *FunctionBody* `}`

`TabularArguments` - [*TabularArgName* `:` `(`[*AtrName* `:` *AtrType*] [`,` ... ]`)`] [`,` ... ][`,`]

 ou :

 [*TabularArgName* `:` `(` `*` `)`] - indiquant des expressions tabulaires avec des colonnes de variables et inconnues.

`ScalarArguments` - [*ArgName* `:` *ArgType*] [`,` ... ]


|Champ  |Définition  |Exemple  |
|---------|---------|---------|
| **view** | Peut apparaître uniquement dans une instruction let sans paramètre, qui n’a pas d’arguments. Lorsque le mot clé « view » est utilisé, l’instruction let est incluse dans les requêtes qui utilisent un opérateur `union` avec la sélection de caractères génériques des tables/vues. |  |
| **_TabularArguments_* _ | Liste des arguments d’expressions tabulaires formels. 
| Chaque argument tabulaire a :||
|<ul><li> _TabularArgName*</li></ul> | Nom de l’argument tabulaire formel. Le nom peut apparaître dans *FunctionBody* et est lié à une valeur particulière lorsque l’expression lambda est appelée. ||
|<ul><li>Définition de schéma de table </li></ul> | Liste d’attributs avec leurs types| AtrName : AtrType|
| ***ScalarArguments** _ | Liste des arguments scalaires formels. 
|Chaque argument scalaire a :||
|<ul><li>_ArgName*</li></ul> | Nom de l’argument scalaire formel. Le nom peut apparaître dans *FunctionBody* et est lié à une valeur particulière lorsque l’expression lambda est appelée.  |
| <ul><li>*ArgType* </li></ul>| Type de l’argument scalaire formel. | Actuellement, seuls les types suivants sont pris en charge comme type d’argument lambda : `bool`, `string`, `long`, `datetime`, `timespan`, `real` et `dynamic` (et les alias de ces types).

> [!NOTE]
>L’expression tabulaire utilisée dans l’appel lambda doit inclure (sans s’y limiter) tous les attributs avec les types correspondants.
>
>`(*)` peut être utilisé en tant qu’expression tabulaire. 
>
> Toute expression tabulaire peut être utilisée dans l’appel lambda et aucune de ses colonnes n’est accessible dans l’expression lambda. 
>
> Tous les arguments tabulaires doivent apparaître avant les arguments scalaires.

## <a name="multiple-and-nested-let-statements"></a>Instructions Let multiples et imbriquées

Plusieurs instructions Let peuvent être utilisées avec le point-virgule (`;`) servant de délimiteur, comme dans l’exemple suivant.

> [!NOTE]
> La dernière instruction doit être une expression de requête valide. 

```kusto
let start = ago(5h); 
let period = 2h; 
T | where Time > start and Time < start + period | ...
```

Les instructions Let imbriquées sont autorisées et peuvent être utilisées dans une expression lambda.
Les instructions et les arguments Let sont visibles dans la portée actuelle et intérieure du corps de la fonction.

```kusto
let start_time = ago(5h); 
let end_time = start_time + 2h; 
T | where Time > start_time and Time < end_time | ...
```

## <a name="examples"></a>Exemples

### <a name="use-let-function-to-define-constants"></a>Utiliser la fonction Let pour définir des constantes

L’exemple suivant lie le nom `x` au littéral scalaire `1`, puis l’utilise dans une instruction d’expression tabulaire.

```kusto
let x = 1;
range y from x to x step x
```

Cet exemple est similaire au précédent, mais seul le nom de l’instruction Let est fourni à l’aide de la notion `['name']`.

```kusto
let ['x'] = 1;
range y from x to x step x
```

### <a name="use-let-for-scalar-values"></a>Utiliser Let pour les valeurs scalaires

```kusto
let n = 10;  // number
let place = "Dallas";  // string
let cutoff = ago(62d); // datetime
Events 
| where timestamp > cutoff 
    and city == place 
| take n
```

### <a name="use-let-statement-with-arguments-for-scalar-calculation"></a>Utiliser l’instruction Let avec des arguments pour le calcul scalaire

L’exemple suivant utilise l’instruction Let avec des arguments pour le calcul scalaire. La requête définit la fonction `MultiplyByN` pour la multiplication de deux nombres.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let MultiplyByN = (val:long, n:long) { val * n };
range x from 1 to 5 step 1 
| extend result = MultiplyByN(x, 5)
```

|x|result|
|---|---|
|1|5|
|2|10|
|3|15|
|4|20|
|5|25|

L’exemple suivant supprime les éléments de début et de fin (`1`) de l’entrée.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let TrimOnes = (s:string) { trim("1", s) };
range x from 10 to 15 step 1 
| extend result = TrimOnes(tostring(x))
```

|x|result|
|---|---|
|10|0|
|11||
|12|2|
|13|3|
|14|4|
|15|5|


### <a name="use-multiple-let-statements"></a>Utiliser plusieurs instructions Let

Cet exemple définit deux instructions Let où une instruction (`foo2`) en utilise une autre (`foo1`).

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo1 = (_start:long, _end:long, _step:long) { range x from _start to _end step _step};
let foo2 = (_step:long) { foo1(1, 100, _step)};
foo2(2) | count
// Result: 50
```

### <a name="use-the-view-keyword-in-a-let-statement"></a>Utiliser le mot clé `view` dans une instruction Let

Cet exemple montre comment utiliser l’instruction Let avec le mot clé `view`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Range10 = view () { range MyColumn from 1 to 10 step 1 };
let Range20 = view () { range MyColumn from 1 to 20 step 1 };
search MyColumn == 5
```

|$table|MyColumn|
|---|---|
|Range10|5|
|Range20|5|


### <a name="use-materialize-function"></a>Utiliser la fonction materialize

La fonction [`materialize`](materializefunction.md) vous permet de mettre en cache les résultats de la sous-requête au moment de l’exécution de la requête. 

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
|2016-05-01 00:00:00.0000000|2016-05-02 00:00:00.0000000|34,0645725975255|
|2016-05-01 00:00:00.0000000|2016-05-03 00:00:00.0000000|16,618368960101|
|2016-05-02 00:00:00.0000000|2016-05-03 00:00:00.0000000|14,6291376489636|
