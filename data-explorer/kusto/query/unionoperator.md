---
title: opérateur syndical - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur syndical dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b62c259e1abf1ff0e0e98a90ac4da5a000db5320
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765407"
---
# <a name="union-operator"></a>opérateur union

Prend deux tables ou plus et retourne les lignes de toutes les tables. 

```kusto
Table1 | union Table2, Table3
```

**Syntaxe**

*T* `| union` [*UnionParameters*`kind=` `inner` | `outer`]`withsource=`[ ]`isfuzzy=` `true` | `false`[*ColumnName*] [ ] *Table* `,` *[Table*]...  

Formulaire alternatif sans entrée canalisation :

`union`[*UnionParameters*] [`kind=` `inner``isfuzzy=` `true` | `false` *Table* `,` *Table**ColumnName*]`withsource=`[ ColumnName ] [ ] Table [Table ]... | `outer`  

**Arguments**

::: zone pivot="azuredataexplorer"

* `Table`:
    *  Nom d’une table, tel que `Events`; ou
    *  Une expression de requête qui doit être jointe `(Events | where id==42)` à `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))`la parenthèse, telle que ou ; Ou
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’union de toutes les `E`tables dans la base de données dont les noms commencent .
* `kind`: 
    * `inner` : le résultat comporte le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
    * `outer`- (par défaut). Le résultat a toutes les colonnes qui se produisent dans l’un des intrants. Les cellules qui n’ont pas été `null`définies par une ligne d’entrée sont définies à .
* `withsource`=*ColumnName*: Si spécifié, la sortie comprendra une colonne appelée *ColumnName* dont la valeur indique quelle table source a contribué chaque ligne.
Si la requête efficacement (après correspondance wildcard) références tables de plus d’une base de données (base de données par défaut compte toujours), la valeur de cette colonne aura un nom de table qualifié avec la base de données.
De même, les qualifications __des clusters et des bases de données__ seront présentes dans la valeur si plus d’un cluster est référencé. 
* `isfuzzy=``true` `isfuzzy` : Si est `true` configuré - permet la résolution floue des jambes syndicales.  |  `false` `Fuzzy`s’applique à `union` l’ensemble des sources. Cela signifie que, tout en analysant la requête et en se préparant à l’exécution, l’ensemble des sources syndicales est réduit à l’ensemble des références de table qui existent et sont accessibles à l’époque. Si au moins un de ces tableaus a été trouvé, toute défaillance de résolution donnera un avertissement dans les résultats de l’état de la requête (un pour chaque référence manquante), mais n’empêchera pas l’exécution de la requête; si aucune résolution n’a été couronnée de succès - la requête retournera une erreur.
Par défaut, il s’agit de `isfuzzy=` `false`.
* *UnionParameters*: Paramètres nuls ou plus (séparés dans l’espace) sous la forme de *La valeur* *de nom* `=` qui contrôlent le comportement de l’opération de match de ligne et du plan d’exécution. Les paramètres suivants sont pris en charge : 

  |Nom           |Valeurs                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Nombre*|Indique au système le nombre de `union` sous-cas simultanés de l’opérateur doivent être exécutés en parallèle. *Défaut*: Quantité de cœurs CPU sur le nœud unique du cluster (2 à 16).|
  |`hint.spread`|*Number*|Indique au système combien de nœuds `union` doivent être utilisés par l’exécution simultanée des sous-ques. *Défaut*: 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  Le nom d’une table, tel que`Events`
    *  Une expression de requête qui doit être entourée de parenthèse,`(Events | where id==42)`
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’union de toutes les `E`tables dans la base de données dont les noms commencent .
* `kind`: 
    * `inner` : le résultat comporte le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
    * `outer`- (par défaut). Le résultat a toutes les colonnes qui se produisent dans l’un des intrants. Les cellules qui n’ont pas été `null`définies par une ligne d’entrée sont définies à .
* `withsource`=*ColumnName*: Si spécifié, la sortie comprendra une colonne appelée *ColumnName* dont la valeur indique quelle table source a contribué chaque ligne.
Si la requête efficacement (après correspondance wildcard) références tables de plus d’une base de données (base de données par défaut compte toujours), la valeur de cette colonne aura un nom de table qualifié avec la base de données.
De même, les qualifications __des clusters et des bases de données__ seront présentes dans la valeur si plus d’un cluster est référencé. 
* `isfuzzy=``true` `isfuzzy` : Si est `true` configuré - permet la résolution floue des jambes syndicales.  |  `false` `Fuzzy`s’applique à `union` l’ensemble des sources. Cela signifie que, tout en analysant la requête et en se préparant à l’exécution, l’ensemble des sources syndicales est réduit à l’ensemble des références de table qui existent et sont accessibles à l’époque. Si au moins un de ces tableaus a été trouvé, toute défaillance de résolution donnera un avertissement dans les résultats de l’état de la requête (un pour chaque référence manquante), mais n’empêchera pas l’exécution de la requête; si aucune résolution n’a été couronnée de succès - la requête retournera une erreur.
Par défaut, il s’agit de `isfuzzy=false`.

::: zone-end

**Retourne**

Une table contenant autant de lignes que dans l’ensemble des tables d’entrée.

**Remarques**

::: zone pivot="azuredataexplorer"

1. `union`la portée peut inclure des [instructions de laisser](./letstatement.md) si celles-ci sont attribuées avec le mot clé de [vue](./letstatement.md)
2. `union`la portée n’inclura pas les [fonctions](../management/functions.md). Pour inclure une fonction dans la portée du syndicat, définissez une [déclaration de laisser](./letstatement.md) avec mot [clé](./letstatement.md)
3. Si `union` l’entrée est [des tableaux](../management/tables.md) (comme `union` opposition aux expressions [tabulaires](./tabularexpressionstatements.md)), et le est suivi par un opérateur [où](./whereoperator.md), pour de meilleures performances, envisager de remplacer les deux par [trouver](./findoperator.md). Notez le [schéma de](./findoperator.md#output-schema) `find` sortie différent produit par l’opérateur. 
4. `isfuzzy=true`ne s’applique qu’à la phase de résolution des `union` sources. Une fois l’ensemble des tables sources déterminées, d’éventuelles défaillances de requête supplémentaires ne seront pas supprimées.
5. Lors `outer union`de l’utilisation , le résultat a toutes les colonnes qui se produisent dans l’une des entrées, une colonne pour chaque nom et les occurrences de type. Cela signifie que si une colonne apparaît dans plusieurs tableaux et a plusieurs `union`types, il aura une colonne correspondante pour chaque type dans le résultat de '. Ce nom de colonne sera suffixe avec un ''' suivi du [type](./scalar-data-types/index.md)de colonne d’origine .

::: zone-end

::: zone pivot="azuremonitor"

1. `union`la portée peut inclure des [instructions de laisser](./letstatement.md) si celles-ci sont attribuées avec le mot clé de [vue](./letstatement.md)
2. `union`n’inclura pas les fonctions. Pour inclure la fonction dans la portée du syndicat - définir une [déclaration de laisser](./letstatement.md) avec mot clé de [vue](./letstatement.md)
3. Si `union` l’entrée est des tableaux (comme `union` opposition aux [expressions tabulaires](./tabularexpressionstatements.md)), et le est suivi par un opérateur [où](./whereoperator.md), envisager de remplacer les deux par [trouver](./findoperator.md) pour de meilleures performances. Veuillez noter le [schéma de](./findoperator.md#output-schema) `find` sortie différent produit par l’opérateur. 
4. `isfuzzy=``true` ne s’applique qu’à la phase de résolution des `union` sources. Une fois l’ensemble des tables sources déterminée, d’éventuelles défaillances de requête supplémentaires ne seront pas supprimées.
5. Lors `outer union`de l’utilisation , le résultat a toutes les colonnes qui se produisent dans l’une des entrées, une colonne pour chaque nom et les occurrences de type. Cela signifie que si une colonne apparaît dans plusieurs tableaux et a plusieurs `union`types, il aura une colonne correspondante pour chaque type dans le résultat de '. Ce nom de colonne sera suffixe avec un ''' suivi du [type](./scalar-data-types/index.md)de colonne d’origine .

::: zone-end


**Exemple**

```kusto
union K* | where * has "Kusto"
```

Lignes de toutes les tables dans `K`la base de données dont `Kusto`le nom commence par , et dans lequel toute colonne comprend le mot .

**Exemple**

```kusto
union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```

Le nombre d’utilisateurs ayant produit un événement `Query` ou un événement `Command` au cours de la journée précédente. Dans le résultat, la colonne ’SourceTable’ indique « Requête » ou « Commande ».

```kusto
Query
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Cette version plus efficace génère le même résultat. Il filtre chaque table avant la création de l’union.

**Exemple : Utilisation`isfuzzy=true`**
 
```kusto     
// Using union isfuzzy=true to access non-existing view:                                     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true
(View_1 | where x > 0), 
(View_2 | where x > 0),
(View_3 | where x > 0)
| count 
```

|Count|
|---|
|2|

Observation de l’état de requête - l’avertissement suivant est retourné:`Failed to resolve entity 'View_3'`

```kusto
// Using union isfuzzy=true and wildcard access:
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true View*, SomeView*, OtherView*
| count 
```

|Count|
|---|
|3|

Observation de l’état de requête - l’avertissement suivant est retourné:`Failed to resolve entity 'SomeView*'`

**Exemple : les colonnes sources tapent un décalage**
 
```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
union withsource=TableName View_1, View_2
```

|TableName|x_long|x_int|
|---------|------|-----|
|View_1   |1     |     |
|View_2   |      |2    |

```kusto     
let View_1 = view () { print x=1 };
let View_2 = view () { print x=toint(2) };
let View_3 = view () { print x_long=3 };
union withsource=TableName View_1, View_2, View_3 
```

|TableName|x_long1|x_int |x_long|
|---------|-------|------|------|
|View_1   |1      |      |      |
|View_2   |       |2     |      |
|View_3   |       |      |3     |

Colonne `x` `View_1` de reçu `_long`le suffixe , `x_long` et comme une colonne nommée existe déjà dans le schéma de résultat, les noms de colonne ont été dé-dupliqués, produisant une nouvelle colonne-`x_long1`
