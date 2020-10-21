---
title: Jonction entre clusters-Azure Explorateur de données
description: Cet article décrit la jonction entre clusters dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: a7c8f89886a8c12941dbc218ad69b35eebd7f1c7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246600"
---
# <a name="cross-cluster-join"></a>Jointure entre clusters

::: zone pivot="azuredataexplorer"

Pour une discussion générale sur les requêtes entre clusters, consultez [requêtes entre clusters ou entre bases de données](cross-cluster-or-database-queries.md) .

Il est possible d’effectuer une opération de jointure sur des jeux de données résidant sur des clusters différents. Par exemple :

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

Dans l’exemple ci-dessus, l’opération de jointure est une jointure entre clusters, en supposant que le cluster actuel n’est pas « SomeCluster » ou « SomeCluster2 ».

Dans l’exemple suivant :

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

l’opération de jointure n’est pas une jointure entre clusters, car ses opérandes proviennent du même cluster.

Quand Kusto rencontre une jointure entre clusters, il détermine automatiquement l’emplacement d’exécution de l’opération de jointure. Cette décision peut avoir l’un des trois résultats possibles :

* Opération d’exécution de jointure sur le cluster de l’opérande de gauche, l’opérande de droite sera d’abord récupéré par ce cluster. (l’exemple de jointure **(1)** sera exécuté sur le cluster local)
* Opération d’exécution de jointure sur le cluster de l’opérande de droite, l’opérande de gauche sera d’abord récupéré par ce cluster. (l’exemple de jointure **(2)** sera exécuté sur « SomeCluster2 »)
* Exécuter une opération de jointure localement (c’est-à-dire sur le cluster qui a reçu la requête), les deux opérandes sont d’abord récupérés par le cluster local.

La décision réelle dépend de la requête spécifique. La stratégie de communication à distance de jointure automatique est (version simplifiée) : «si l’un des opérandes est local, Join sera exécuté localement. Si les deux opérandes sont distants, la jointure est exécutée sur le cluster de l’opérande de droite».

Parfois, les performances de la requête peuvent être améliorées si la stratégie de communication à distance automatique n’est pas respectée. Dans ce cas, exécutez l’opération JOIN sur le cluster de l’opérande le plus grand.

Si, par exemple **(1)** , le jeu de données produit par `T | ...` est bien plus petit que celui produit par `cluster("SomeCluster").database("SomeDB").T2 | ...` , il est plus efficace d’exécuter l’opération de jointure sur « SomeCluster ».

Cette opération peut être effectuée en donnant à Kusto Join l’indicateur de communication à distance. La syntaxe est :

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

Voici les valeurs autorisées pour `strategy`
* `left` -exécuter Join sur le cluster de l’opérande gauche 
* `right` -exécuter Join sur le cluster de l’opérande de droite
* `local` -exécuter Join sur le cluster du cluster actif
* `auto` -(par défaut) laisser Kusto prendre la décision de communication à distance automatique

> [!Note]
> L’indicateur de communication à distance Join sera ignoré par Kusto si la stratégie avec indicateur n’est pas applicable à l’opération de jointure.

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
