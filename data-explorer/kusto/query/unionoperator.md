---
title: Opérateur union – Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’opérateur union dans Azure Data Explorer.
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
ms.openlocfilehash: 3ce7c2c09e9cd5449accfabc2a7e1cc21e4ed339
ms.sourcegitcommit: d4b359e817e002fba7320132732ce6d9cee97415
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541476"
---
# <a name="union-operator"></a>opérateur union

Prend deux tables ou plus et retourne les lignes de toutes les tables. 

```kusto
Table1 | union Table2, Table3
```

## <a name="syntax"></a>Syntaxe

*T* `| union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

Autre forme sans entrée canalisée :

`union` [*UnionParameters*] [`kind=` `inner`|`outer`] [`withsource=`*ColumnName*] [`isfuzzy=` `true`|`false`] *Table* [`,` *Table*]...  

## <a name="arguments"></a>Arguments

::: zone pivot="azuredataexplorer"

* `Table`:
    *  Nom d’une table, tel que `Events`; ou
    *  Expression de requête qui doit être placée entre parenthèses, par exemple, `(Events | where id==42)` ou `(cluster("https://help.kusto.windows.net:443").database("Samples").table("*"))` ; ou
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’union de toutes les tables de la base de données dont le nom commence par `E`.
* `kind`: 
    * `inner` : le résultat comporte le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
    * `outer` : (par défaut). Le résultat contient toutes les colonnes qui apparaissent dans les entrées. Les cellules qui n’ont pas été définies par une ligne d’entrée ont la valeur `null`.
* `withsource`=*ColumnName* : si spécifié, la sortie contient une colonne nommée *ColumnName*, dont la valeur indique la table source de chaque ligne.
Si la requête (après mise en correspondance de caractère générique) référence des tables de plusieurs bases de données (la base de données par défaut compte toujours), la valeur de cette colonne aura un nom de table qualifié avec la base de données.
De même, des qualifications de __cluster et base de données__ seront présentes dans la valeur si plusieurs clusters sont référencés. 
* `isfuzzy=` `true` | `false` : si `isfuzzy` est défini sur `true`, permet la résolution approximative des segments de l’union. `Fuzzy` s’applique à l’ensemble de sources de l’`union` . Cela signifie que, lors de l’analyse de la requête et de la préparation de l’exécution, l’ensemble des sources de l’union est réduit à l’ensemble des références de table qui existent et sont accessibles à ce moment-là. Si au moins une table de ce type a été trouvée, tout échec de résolution génère un avertissement dans les résultats d’état de la requête (un pour chaque référence manquante), mais n’empêche pas l’exécution de la requête. Si aucune résolution n’a réussi, la requête retourne une erreur.
La valeur par défaut est `isfuzzy=` `false`.
* *UnionParameters* : zéro ou plusieurs paramètres (séparés par des espaces) sous la forme *Nom* `=` *Valeur*, qui contrôlent le comportement de l’opération de correspondance des lignes et du plan d’exécution. Les paramètres suivants sont pris en charge : 

  |Nom           |Valeurs                                        |Description                                  |
  |---------------|----------------------------------------------|---------------------------------------------|
  |`hint.concurrency`|*Nombre*|Indique au système le nombre de sous-requêtes simultanées de l’opérateur `union` qui doivent être exécutées en parallèle. *Par défaut* : quantité de cœurs de processeur sur le nœud unique du cluster (2 à 16).|
  |`hint.spread`|*Nombre*|Indique au système le nombre de nœuds que doit utiliser l’exécution de sous-requêtes `union` simultanées. *Par défaut* : 1.|

::: zone-end

::: zone pivot="azuremonitor"

* `Table`:
    *  Nom d’une table, par exemple, `Events`
    *  Expression de requête qui doit être placée entre parenthèses, par exemple `(Events | where id==42)`
    *  Ensemble de tables spécifié par un caractère générique. Par exemple, `E*` formerait l’union de toutes les tables de la base de données dont le nom commence par `E`.

> [!NOTE]
> Quand la liste de tables est connue, évitez d’utiliser des caractères génériques. Certains espaces de travail contiennent un très grand nombre de tables susceptibles d’aboutir à une exécution inefficace. Les tables peuvent également être ajoutées au fur et à mesure et aboutir à des résultats imprévisibles.
    
* `kind`: 
    * `inner` : le résultat comporte le sous-ensemble de colonnes qui sont communes à toutes les tables d’entrée.
    * `outer` : (par défaut). Le résultat contient toutes les colonnes qui apparaissent dans les entrées. Les cellules qui n’ont pas été définies par une ligne d’entrée ont la valeur `null`.
* `withsource`=*ColumnName* : si spécifié, la sortie contient une colonne nommée *ColumnName*, dont la valeur indique la table source de chaque ligne.
Si la requête (après mise en correspondance de caractère générique) référence des tables de plusieurs bases de données (la base de données par défaut compte toujours), la valeur de cette colonne aura un nom de table qualifié avec la base de données.
De même, les qualifications de __cluster et base de données__ seront présentes dans la valeur si plusieurs clusters sont référencés. 
* `isfuzzy=` `true` | `false` : si `isfuzzy` est défini sur `true`, permet la résolution approximative des segments de l’union. `Fuzzy` s’applique à l’ensemble de sources de l’`union` . Cela signifie que, lors de l’analyse de la requête et de la préparation de l’exécution, l’ensemble des sources de l’union est réduit à l’ensemble des références de table qui existent et sont accessibles à ce moment-là. Si au moins une table de ce type a été trouvée, tout échec de résolution génère un avertissement dans les résultats d’état de la requête (un pour chaque référence manquante), mais n’empêche pas l’exécution de la requête. Si aucune résolution n’a réussi, la requête retourne une erreur.
La valeur par défaut est `isfuzzy=false`.

::: zone-end

## <a name="returns"></a>Retours

Une table contenant autant de lignes que dans l’ensemble des tables d’entrée.

**Remarques**

::: zone pivot="azuredataexplorer"

1. L’étendue de l’`union` peut inclure des [instructions let](./letstatement.md) si celles-ci sont attribuées avec le [mot clé view](./letstatement.md)
2. L’étendue de l’`union` n’inclut pas de [fonctions](../management/functions.md). Pour inclure une fonction dans l’étendue de l’union, définissez une [instruction let](./letstatement.md) avec le [mot clé view](./letstatement.md).
3. Si l’entrée de l’`union` est constituée de [tables](../management/tables.md) (à l’opposé d’[expressions tabulaires](./tabularexpressionstatements.md)), et que l’`union` est suivie d’un [opérateur where](./whereoperator.md), pour améliorer les performances, envisagez de remplacer les deux par l’opérateur [find](./findoperator.md). Notez le [schéma de sortie](./findoperator.md#output-schema) différent produit par l’opérateur `find`. 
4. `isfuzzy=true` s’applique uniquement à la phase de résolution des sources de l’`union`. Une fois l’ensemble des tables sources déterminé, les éventuels échecs de requête ne sont pas supprimés.
5. Lors de l’utilisation de `outer union`, le résultat contient toutes les colonnes qui se trouvent dans l’une des entrées, une colonne pour chaque occurrence de nom et de type. Cela signifie que, si une colonne apparaît dans plusieurs tables et a plusieurs types, elle aura une colonne correspondante pour chaque type dans le résultat de l’`union`. Ce nom de colonne sera assorti du suffixe « _ », suivi de la colonne d’origine [type](./scalar-data-types/index.md).

::: zone-end

::: zone pivot="azuremonitor"

1. L’étendue de l’`union` peut inclure des [instructions let](./letstatement.md) si celles-ci sont attribuées avec le [mot clé view](./letstatement.md)
2. L’étendue de l’`union` n’inclut pas de fonctions. Pour inclure une fonction dans l’étendue de l’union, définissez une [instruction let](./letstatement.md) avec le [mot clé view](./letstatement.md).
3. Si l’entrée de l’`union` est constituée tables (à l’opposé d’[expressions tabulaires](./tabularexpressionstatements.md)), et que l’`union` est suivie d’un [opérateur where](./whereoperator.md), envisagez de remplacer les deux par l’opérateur [fin](./findoperator.md) pour améliorer les performances. Veuillez noter le [schéma de sortie](./findoperator.md#output-schema) différent produit par l’opérateur `find`. 
4. `isfuzzy=` `true` s’applique uniquement à la phase de résolution des sources de l’`union`. Une fois l’ensemble des tables sources déterminé, les éventuels échecs de requête ne sont pas supprimés.
5. Lors de l’utilisation de `outer union`, le résultat contient toutes les colonnes qui se trouvent dans l’une des entrées, une colonne pour chaque occurrence de nom et de type. Cela signifie que, si une colonne apparaît dans plusieurs tables et a plusieurs types, elle aura une colonne correspondante pour chaque type dans le résultat de l’`union`. Ce nom de colonne sera assorti du suffixe « _ », suivi de la colonne d’origine [type](./scalar-data-types/index.md).

::: zone-end


## <a name="example-tables-with-string-in-name-or-column"></a>Exemple : Tables avec une chaîne dans le nom ou la colonne

```kusto
union K* | where * has "Kusto"
```

Lignes extraites de toutes les tables de la base de données dont le nom commence par `K` et dans lesquelles une colonne contient le mot `Kusto`.

## <a name="example-distinct-count"></a>Exemple : Nombre distinct

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

**Exemple : Utilisation de `isfuzzy=true`**
 
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

|Nombre|
|---|
|2|

Observation de l’état de la requête – l’avertissement suivant a été retourné : `Failed to resolve entity 'View_3'`

```kusto
// Using union isfuzzy=true and wildcard access:
let View_1 = view () { print x=1 };
let View_2 = view () { print x=1 };
let OtherView_1 = view () { print x=1 };
union isfuzzy=true View*, SomeView*, OtherView*
| count 
```

|Nombre|
|---|
|3|

Observation de l’état de la requête – l’avertissement suivant a été retourné : `Failed to resolve entity 'SomeView*'`

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

La colonne `x` de `View_1` a reçu le suffixe `_long`, et comme une colonne nommée `x_long` existe déjà dans le schéma de résultat, les noms de colonne ont été dédupliqués, ce qui génère une nouvelle colonne- `x_long1`
