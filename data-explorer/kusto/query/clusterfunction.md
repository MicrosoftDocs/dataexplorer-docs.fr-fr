---
title: cluster () (fonction Scope)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit cluster () (fonction Scope) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 092915c08b4b3d1e72722a4303e911403b2defd2
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737196"
---
# <a name="cluster-scope-function"></a>cluster () (fonction Scope)

::: zone pivot="azuredataexplorer"

Modifie la référence de la requête sur un cluster distant. 

```kusto
cluster('help').database('Sample').SomeTable
```

**Syntaxe**

`cluster(`*stringConstant*`)`

**Arguments**

* *stringConstant*: nom du cluster qui est référencé. Le nom du cluster peut être un nom DNS complet ou une chaîne avec `.kusto.windows.net`un suffixe. L’argument doit être _constant_ avant l’exécution de la requête, ce qui signifie qu’il ne peut pas provenir de l’évaluation de la sous-requête.

**Remarques**

* Pour accéder à la base de données dans la même fonction [de base de données de cluster ()](databasefunction.md) .
* Plus d’informations sur les requêtes entre clusters et les bases de données croisées disponibles [ici](cross-cluster-or-database-queries.md)  

## <a name="examples"></a>Exemples

### <a name="use-cluster-to-access-remote-cluster"></a>Utiliser le cluster () pour accéder au cluster distant 

La requête suivante peut être exécutée sur n’importe quel cluster Kusto.

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>Utilisation du cluster () à l’intérieur des instructions Let 

La même requête que ci-dessus peut être réécrite pour utiliser la fonction inline (instruction Let) `clusterName` qui reçoit un paramètre, qui est transmis à la fonction cluster ().

```kusto
let foo = (clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
foo('help')
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-functions"></a>Utilisation du cluster () à l’intérieur des fonctions 

La même requête que ci-dessus peut être réécrite pour être utilisée dans une fonction qui `clusterName` reçoit un paramètre, qui est transmis à la fonction cluster ().

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
