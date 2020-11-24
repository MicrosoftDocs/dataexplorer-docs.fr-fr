---
title: opérateur Union-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur Union dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b8ad39e8c1233acc2df6c30059a6926cea85f37a
ms.sourcegitcommit: 4e811d2f50d41c6e220b4ab1009bb81be08e7d84
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95512806"
---
# <a name="union-operator"></a>opérateur union

Prend deux tables ou plus et retourne les lignes de toutes les tables. 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>Syntaxe

*T* `| union` [*UnionParameters*] [ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ] *table* [ `,` *table*]...  

Formulaire de remplacement sans entrée dirigée :

`union`[*UnionParameters*] [ `kind=` `inner` | `outer` ] [ `withsource=` *ColumnName*] [ `isfuzzy=` `true` | `false` ] *table* [ `,` *table*]...  

## <a name="arguments"></a>Arguments

::: zone pivot="azuredataexplorer"

* `Table`:
    *  Nom d’une table, tel que `Events`; ou
    *  Expression de requête qui doit être placée entre parenthèses, telle que `(Events | where id==42)` ou `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))` ; ou
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’Union de toutes les tables de la base de données dont les noms commencent `E` .
* `kind`: 
    * `inner` : le résultat comporte le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
    * `outer` -(valeur par défaut). Le résultat contient toutes les colonnes qui se trouvent dans l’une des entrées. Les cellules qui n’ont pas été définies par une ligne d’entrée ont la valeur `null` .
* `withsource`=*ColumnName*: si elle est spécifiée, la sortie inclut une colonne appelée *ColumnName* dont la valeur indique la table source qui a participé à chaque ligne.
Si la requête (après correspondance avec caractère générique) fait référence à des tables de plusieurs bases de données (la base de données par défaut compte toujours), la valeur de cette colonne aura un nom de table qualifié avec la base de données.
De même, les compétences __de cluster et de base de données__ sont présentes dans la valeur si plusieurs clusters sont référencés. 
* `isfuzzy=``true`  |  `false` : Si `isfuzzy` a la valeur `true` -permet la résolution approximative des jambes d’Union. `Fuzzy` s’applique à l’ensemble de `union` sources. Cela signifie que lors de l’analyse de la requête et de la préparation de l’exécution, l’ensemble de sources d’Union est réduit à l’ensemble des références de table qui existent et sont accessibles à ce moment-là. Si au moins une table de ce type a été trouvée, tout échec de résolution génère un avertissement dans les résultats de l’état de la requête (un pour chaque référence manquante), mais n’empêche pas l’exécution de la requête. Si aucune résolution n’a réussi, la requête renverra une erreur.
La valeur par défaut est `isfuzzy=` `false`.
* *UnionParameters*: zéro ou plusieurs paramètres (séparés par des espaces) sous la forme d’une valeur de *nom* `=` *Value* qui contrôlent le comportement de l’opération de correspondance des lignes et du plan d’exécution. Les paramètres suivants sont pris en charge : 

  |Nom           |Valeurs                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Nombre*|Indique au système le nombre de sous-requêtes simultanées de l' `union` opérateur qui doivent être exécutées en parallèle. *Valeur par défaut*: quantité de cœurs de processeur sur le nœud unique du cluster (2 à 16).|
  |`hint.spread`|*Nombre*|Indique au système le nombre de nœuds qui doivent être utilisés par l’exécution simultanée des sous- `union` requêtes. *Valeur par défaut*: 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  Nom d’une table, tel que `Events`
    *  Expression de requête qui doit être placée entre parenthèses, par exemple `(Events | where id==42)`
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’Union de toutes les tables de la base de données dont les noms commencent `E` .
* `kind`: 
    * `inner` : le résultat comporte le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
    * `outer` -(valeur par défaut). Le résultat contient toutes les colonnes qui se trouvent dans l’une des entrées. Les cellules qui n’ont pas été définies par une ligne d’entrée ont la valeur `null` .
* `withsource`=*ColumnName*: si elle est spécifiée, la sortie inclut une colonne appelée *ColumnName* dont la valeur indique la table source qui a apporté chaque ligne.
Si la requête (après correspondance avec caractère générique) fait référence à des tables de plusieurs bases de données (la base de données par défaut compte toujours), la valeur de cette colonne aura un nom de table qualifié avec la base de données.
De même, les compétences du __cluster et de la base de données__ sont présentes dans la valeur si plusieurs clusters sont référencés. 
* `isfuzzy=``true`  |  `false` : Si `isfuzzy` a la valeur `true` -permet la résolution approximative des jambes d’Union. `Fuzzy` s’applique à l’ensemble de `union` sources. Cela signifie que lors de l’analyse de la requête et de la préparation de l’exécution, l’ensemble de sources d’Union est réduit à l’ensemble des références de table qui existent et sont accessibles à ce moment-là. Si au moins une table de ce type a été trouvée, tout échec de résolution génère un avertissement dans les résultats de l’état de la requête (un pour chaque référence manquante), mais n’empêche pas l’exécution de la requête. Si aucune résolution n’a réussi, la requête renverra une erreur.
La valeur par défaut est `isfuzzy=false`.

::: zone-end

## <a name="returns"></a>Retours

Une table contenant autant de lignes que dans l’ensemble des tables d’entrée.

**Remarques**

::: zone pivot="azuredataexplorer"

1. `union` l’étendue peut inclure des [instructions Let](./letstatement.md) si celles-ci sont attribuées avec le [mot clé View](./letstatement.md)
2. `union` l’étendue n’inclut pas de [fonctions](../management/functions.md). Pour inclure une fonction dans l’étendue d’Union, définissez une [instruction Let](./letstatement.md) avec le [mot clé View](./letstatement.md)
3. Si l' `union` entrée correspond à des [tables](../management/tables.md) (comme s’il s’opposent à des [expressions tabulaires](./tabularexpressionstatements.md)), et que le `union` est suivi d’un [opérateur WHERE](./whereoperator.md), pour de meilleures performances, envisagez de remplacer les deux par [Find](./findoperator.md). Notez le [schéma de sortie](./findoperator.md#output-schema) différent produit par l' `find` opérateur. 
4. `isfuzzy=true` s’applique uniquement à la `union` phase de résolution des sources. Une fois l’ensemble des tables sources déterminé, les échecs de requête supplémentaires éventuels ne sont pas supprimés.
5. Lorsque vous utilisez `outer union` , le résultat contient toutes les colonnes qui se trouvent dans l’une des entrées, une colonne pour chaque nom et des occurrences de type. Cela signifie que si une colonne apparaît dans plusieurs tables et a plusieurs types, elle aura une colonne correspondante pour chaque type dans le `union` résultat de. Ce nom de colonne sera accompagné d’un suffixe « _ » suivi du [type](./scalar-data-types/index.md)de colonne d’origine.

::: zone-end

::: zone pivot="azuremonitor"

1. `union` l’étendue peut inclure des [instructions Let](./letstatement.md) si celles-ci sont attribuées avec le [mot clé View](./letstatement.md)
2. `union` l’étendue n’inclut pas de fonctions. Pour inclure la fonction dans l’étendue Union-définir une [instruction Let](./letstatement.md) avec le [mot clé View](./letstatement.md)
3. Si l' `union` entrée correspond à des tables (comme s’il s’opposent à des [expressions tabulaires](./tabularexpressionstatements.md)), et que le `union` est suivi d’un [opérateur WHERE](./whereoperator.md), envisagez de remplacer les deux par [Find](./findoperator.md) pour obtenir de meilleures performances. Veuillez noter le [schéma de sortie](./findoperator.md#output-schema) différent produit par l' `find` opérateur. 
4. `isfuzzy=``true`s’applique uniquement à la phase de la `union` résolution des sources. Une fois l’ensemble des tables sources déterminé, les échecs de requête supplémentaires éventuels ne sont pas supprimés.
5. Lorsque vous utilisez `outer union` , le résultat contient toutes les colonnes qui se trouvent dans l’une des entrées, une colonne pour chaque nom et des occurrences de type. Cela signifie que si une colonne apparaît dans plusieurs tables et a plusieurs types, elle aura une colonne correspondante pour chaque type dans le `union` résultat de. Ce nom de colonne sera accompagné d’un suffixe « _ » suivi du [type](./scalar-data-types/index.md)de colonne d’origine.

::: zone-end


## <a name="example-tables-with-string-in-name-or-column"></a>Exemple : tables avec chaîne dans le nom ou la colonne

```kusto
union K* | where * has "Kusto"
```

Lignes de toutes les tables de la base de données dont le nom commence par `K` et dans laquelle toute colonne contient le mot `Kusto` .

## <a name="example-distinct-count"></a>Exemple : compte distinct

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

**Exemple : utilisation `isfuzzy=true`**
 
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

Observation de l’état de la requête-l’avertissement suivant a été retourné : `Failed to resolve entity 'View_3'`

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

Observation de l’état de la requête-l’avertissement suivant a été retourné : `Failed to resolve entity 'SomeView*'`

**Exemple : incompatibilité des types de colonnes sources**
 
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

La colonne `x` de `View_1` a reçu le suffixe `_long` et, comme une colonne nommée `x_long` existe déjà dans le schéma de résultat, les noms de colonne ont été dédupliqués, produisant une nouvelle colonne- `x_long1`
