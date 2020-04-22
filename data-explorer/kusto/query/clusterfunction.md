---
title: cluster() (fonction de portée) - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le cluster () (fonction de portée) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c5f537b47006af4035c9db26388c1d4110c69b55
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766102"
---
# <a name="cluster-scope-function"></a>cluster() (fonction de portée)

::: zone pivot="azuredataexplorer"

Modifie la référence de la requête à un cluster distant. 

```kusto
cluster('help').database('Sample').SomeTable
```

**Syntaxe**

`cluster(`*stringConstant*`)`

**Arguments**

* *stringConstant*: Nom du cluster référencé. Nom de cluster peut être soit un nom DNS entièrement qualifié, ou une chaîne qui sera suffixe avec `.kusto.windows.net`. L’argument doit être _constant_ avant l’exécution de la requête, c’est-à-dire ne peut pas provenir d’une sous-évaluation de la requête.

**Remarques**

* Pour accéder à la base de données dans le même cluster - utiliser [la fonction de base de données()](databasefunction.md) .
* Plus d’informations sur les requêtes inter-cluster et cross-database disponibles [ici](cross-cluster-or-database-queries.md)  

## <a name="examples"></a>Exemples

### <a name="use-cluster-to-access-remote-cluster"></a>Utiliser le cluster() pour accéder à un cluster distant 

La requête suivante peut être exécutée sur n’importe lequel des clusters De Kusto.

```kusto
cluster('help').database('Samples').StormEvents | count

cluster('help.kusto.windows.net').database('Samples').StormEvents | count  
```

|Count|
|---|
|59066|

### <a name="use-cluster-inside-let-statements"></a>Utiliser cluster() à l’intérieur laisser les instructions 

La même requête que ci-dessus peut être réécrite pour utiliser `clusterName` la fonction en ligne (laisser l’instruction) qui reçoit un paramètre - qui est passé dans la fonction cluster() .

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

### <a name="use-cluster-inside-functions"></a>Utiliser cluster() à l’intérieur des fonctions 

La même requête que ci-dessus peut être réécrite pour `clusterName` être utilisée dans une fonction qui reçoit un paramètre - qui est passé dans la fonction cluster() .

```kusto
.create function foo(clusterName:string)
{
    cluster(clusterName).database('Samples').StormEvents | count
};
```

**Remarque :** ces fonctions ne peuvent être utilisées que localement et non dans la requête à grappes croisées.

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
