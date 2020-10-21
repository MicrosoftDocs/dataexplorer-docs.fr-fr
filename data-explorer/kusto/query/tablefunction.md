---
title: table () (fonction Scope)-Azure Explorateur de données
description: Cet article décrit le tableau () (fonction Scope) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 80035e20110b8a1f2fb705ef73f75275f60fcdda
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251301"
---
# <a name="table-scope-function"></a>table () (fonction Scope)

La fonction table () référence une table en fournissant son nom en tant qu’expression de type `string` .

```kusto
table('StormEvent')
```

## <a name="syntax"></a>Syntaxe

`table``(` *TableName* [ `,` *Datascope*]`)`

## <a name="arguments"></a>Arguments

::: zone pivot="azuredataexplorer"

* *TableName*: expression de type `string` qui fournit le nom de la table référencée. La valeur de cette expression doit être constante au moment de l’appel à la fonction (autrement dit, elle ne peut pas varier en fonction du contexte de données).

* *Datascope*: paramètre facultatif de type `string` qui peut être utilisé pour restreindre la référence de table aux données en fonction de la façon dont ces données sont placées dans la [stratégie de cache](../management/cachepolicy.md)effective de la table. Si elle est utilisée, l’argument réel doit être une `string` expression constante qui a l’une des valeurs possibles suivantes :

    - `"hotcache"`: Seules les données classées en tant que cache à chaud sont référencées.
    - `"all"`: Toutes les données de la table seront référencées.
    - `"default"`: C’est identique à `"all"` , sauf si le cluster a été configuré pour être utilisé par `"hotcache"` défaut par l’administrateur de cluster.

::: zone-end

::: zone pivot="azuremonitor"

* *TableName*: expression de type `string` qui fournit le nom de la table référencée. La valeur de cette expression doit être constante au moment de l’appel à la fonction (autrement dit, elle ne peut pas varier en fonction du contexte de données).

* *Datascope*: paramètre facultatif de type `string` qui peut être utilisé pour restreindre la référence de table aux données en fonction de la façon dont ces données sont placées dans la stratégie de cache effective de la table. Si elle est utilisée, l’argument réel doit être une `string` expression constante qui a l’une des valeurs possibles suivantes :

    - `"hotcache"`: Seules les données classées en tant que cache à chaud sont référencées.
    - `"all"`: Toutes les données de la table seront référencées.
    - `"default"`: Il est identique à `"all"` .

::: zone-end

## <a name="examples"></a>Exemples

### <a name="use-table-to-access-table-of-the-current-database"></a>Utilisez table () pour accéder à la table de la base de données active

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
table('StormEvent') | count
```

|Nombre|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>Use table () à l’intérieur des instructions Let

La même requête que ci-dessus peut être réécrite pour utiliser la fonction inline (instruction Let) qui reçoit un paramètre `tableName` , qui est transmis à la fonction table ().

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let foo = (tableName:string)
{
    table(tableName) | count
};
foo('help')
```

|Nombre|
|---|
|59066|

### <a name="use-table-inside-functions"></a>Utilisation de table () à l’intérieur des fonctions

La même requête que ci-dessus peut être réécrite pour être utilisée dans une fonction qui reçoit un paramètre `tableName` , qui est transmis à la fonction table ().

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**Remarque :** ces fonctions peuvent être utilisées uniquement localement et non dans la requête entre clusters.

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>Use table () avec un paramètre non constant

Un paramètre, qui n’est pas une chaîne constante scalaire, ne peut pas être passé en tant que paramètre à la `table()` fonction.

Vous trouverez ci-dessous un exemple de solution de contournement pour ce cas de figure.

```kusto
let T1 = print x=1;
let T2 = print x=2;
let _choose = (_selector:string)
{
    union
    (T1 | where _selector == 'T1'),
    (T2 | where _selector == 'T2')
};
_choose('T2')

```

|x|
|---|
|2|
