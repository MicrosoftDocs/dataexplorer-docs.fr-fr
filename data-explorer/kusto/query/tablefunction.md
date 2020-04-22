---
title: table() (fonction de portée) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le tableau () (fonction de portée) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 233baebaf51cdc8b07cdd32cec9bd10a54695c1c
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766159"
---
# <a name="table-scope-function"></a>table() (fonction de portée)

La fonction table() fait référence à une table `string`en fournissant son nom comme expression de type .

```kusto
table('StormEvent')
```

**Syntaxe**

`table``(` *TableName* `,` [ *DataScope*]`)`

**Arguments**

::: zone pivot="azuredataexplorer"

* *TableName*: Une `string` expression de type qui fournit le nom du tableau référencé. La valeur de cette expression doit être constante au point d’appel de la fonction (c’est-à-dire qu’elle ne peut pas varier selon le contexte des données).

* *DataScope*: Un paramètre optionnel de type `string` qui peut être utilisé pour limiter la référence de table aux données en fonction de la façon dont ces données relèvent de la politique de [cache](../management/cachepolicy.md)efficace de la table . S’il est utilisé, l’argument réel doit être une expression constante `string` ayant l’une des valeurs possibles suivantes :

    - `"hotcache"`: Seules les données classées comme cache chaude seront référencées.
    - `"all"`: Toutes les données du tableau seront référencées.
    - `"default"`: C’est `"all"`la même chose que , `"hotcache"` sauf si le cluster a été configuré pour utiliser comme par défaut par l’administrateur cluster.

::: zone-end

::: zone pivot="azuremonitor"

* *TableName*: Une `string` expression de type qui fournit le nom du tableau référencé. La valeur de cette expression doit être constante au point d’appel de la fonction (c’est-à-dire qu’elle ne peut pas varier selon le contexte des données).

* *DataScope*: Un paramètre optionnel de type `string` qui peut être utilisé pour limiter la référence de table aux données en fonction de la façon dont ces données relèvent de la stratégie de cache efficace de la table. S’il est utilisé, l’argument réel doit être une expression constante `string` ayant l’une des valeurs possibles suivantes :

    - `"hotcache"`: Seules les données classées comme cache chaude seront référencées.
    - `"all"`: Toutes les données du tableau seront référencées.
    - `"default"`: C’est `"all"`la même chose que .

::: zone-end

## <a name="examples"></a>Exemples

### <a name="use-table-to-access-table-of-the-current-database"></a>Utiliser la table() pour accéder à la table de base de données actuelle

```kusto
table('StormEvent') | count
```

|Count|
|---|
|59066|

### <a name="use-table-inside-let-statements"></a>Utiliser la table() à l’intérieur laisser les déclarations

La même requête que ci-dessus peut être réécrite pour utiliser `tableName` la fonction en ligne (laisser l’instruction) qui reçoit un paramètre - qui est passé dans la fonction de table() .

```kusto
let foo = (tableName:string)
{
    table(tableName) | count
};
foo('help')
```

|Count|
|---|
|59066|

### <a name="use-table-inside-functions"></a>Utiliser la table() à l’intérieur des fonctions

La même requête que ci-dessus peut être réécrite pour `tableName` être utilisée dans une fonction qui reçoit un paramètre - qui est passé dans la fonction de table() .

```kusto
.create function foo(tableName:string)
{
    table(tableName) | count
};
```

::: zone pivot="azuredataexplorer"

**Remarque :** ces fonctions ne peuvent être utilisées que localement et non dans la requête à grappes croisées.

::: zone-end

### <a name="use-table-with-non-constant-parameter"></a>Utiliser la table() avec un paramètre non constant

Un paramètre, qui n’est pas scalaire chaîne `table()` constante ne peut pas être passé comme paramètre de fonction.

Ci-dessous, étant donné un exemple de solution de contournement pour un tel cas.

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
