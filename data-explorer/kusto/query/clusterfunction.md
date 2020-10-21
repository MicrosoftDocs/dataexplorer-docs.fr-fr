---
title: cluster () (fonction Scope)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit cluster () (fonction Scope) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b2f9dd3fd3eede1f6d527c97b905353f9a331620
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253067"
---
# <a name="cluster-scope-function"></a>cluster () (fonction Scope)

::: zone pivot="azuredataexplorer"

Modifie la référence de la requête sur un cluster distant. 

```kusto
cluster('help').database('Sample').SomeTable
```

## <a name="syntax"></a>Syntaxe

`cluster(`*stringConstant*`)`

## <a name="arguments"></a>Arguments

* *stringConstant*: nom du cluster qui est référencé. Le nom du cluster peut être un nom DNS complet ou une chaîne avec un suffixe `.kusto.windows.net` . L’argument doit être _constant_ avant l’exécution de la requête, ce qui signifie qu’il ne peut pas provenir de l’évaluation de la sous-requête.

**Notes**

* Pour accéder à la base de données dans la même fonction [de base de données de cluster ()](databasefunction.md) .
* Plus d’informations sur les requêtes entre clusters et les bases de données croisées disponibles [ici](cross-cluster-or-database-queries.md)  

## <a name="examples"></a>Exemples

### <a name="use-cluster-to-access-remote-cluster"></a>Utiliser le cluster () pour accéder au cluster distant 

La requête suivante peut être exécutée sur n’importe quel cluster Kusto.

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Nombre|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>Utilisation du cluster () à l’intérieur des instructions Let 

La même requête que ci-dessus peut être réécrite pour utiliser la fonction inline (instruction Let) qui reçoit un paramètre `clusterName` , qui est transmis à la fonction cluster ().

```kusto
let foo = (clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
foo('help')
```

|Nombre|
|---|
|59066|

### <a name="use-cluster-inside-functions"></a>Utilisation du cluster () à l’intérieur des fonctions 

La même requête que ci-dessus peut être réécrite pour être utilisée dans une fonction qui reçoit un paramètre `clusterName` , qui est transmis à la fonction cluster ().

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**Remarque :** ces fonctions peuvent être utilisées uniquement localement et non dans la requête entre clusters.

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
