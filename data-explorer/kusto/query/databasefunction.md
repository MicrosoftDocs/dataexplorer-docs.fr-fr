---
title: base de données () (fonction Scope)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la base de données () (fonction Scope) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e3f874ecfc0bb1872f08efa3269c73b02971e4f3
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737638"
---
# <a name="database-scope-function"></a>base de données () (fonction Scope)

::: zone pivot="azuredataexplorer"

Modifie la référence de la requête en une base de données spécifique au sein de l’étendue du cluster. 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

**Syntaxe**

`database(`*stringConstant*`)`

**Arguments**

* *stringConstant*: nom de la base de données référencée. La base de données identifiée peut être `DatabaseName` ou `PrettyName`. L’argument doit être _constant_ avant l’exécution de la requête, c’est-à-dire qu’il ne peut pas provenir d’une évaluation de sous-requête.

**Remarques**

* Pour accéder au cluster distant et à la base de données distante, consultez fonction de l’étendue [cluster ()](clusterfunction.md) .
* Plus d’informations sur les requêtes entre clusters et les bases de données croisées disponibles [ici](cross-cluster-or-database-queries.md)

## <a name="examples"></a>Exemples

### <a name="use-database-to-access-table-of-other-database"></a>Utilisez la base de données () pour accéder à la table d’une autre base de données. 

```kusto
database('Samples').StormEvents | count
```

|Count|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>Use Database () à l’intérieur des instructions Let 

La même requête que ci-dessus peut être réécrite pour utiliser la fonction inline (instruction Let) `dbName` qui reçoit un paramètre, qui est transmis à la fonction Database ().

```kusto
let foo = (dbName:string)
{
    database(dbName).StormEvents | count
};
foo('help')
```

|Count|
|---|
|59066|

### <a name="use-database-inside-functions"></a>Utiliser la base de données () à l’intérieur des fonctions 

La même requête que ci-dessus peut être réécrite pour être utilisée dans une fonction qui `dbName` reçoit un paramètre, qui est transmis à la fonction Database ().

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

**Remarque :** ces fonctions peuvent être utilisées uniquement localement et non dans la requête entre clusters.

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
