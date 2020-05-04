---
title: Jonction entre clusters-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la jonction entre clusters dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: bd2ebaa35de1997a96c6646c0fe0f7e248af2240
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737315"
---
# <a name="cross-cluster-join"></a>Jointure entre plusieurs clusters

::: zone pivot="azuredataexplorer"

Pour une discussion générale sur les requêtes entre clusters, consultez [requêtes entre clusters ou des bases de données croisées](cross-cluster-or-database-queries.md)

Il est possible d’effectuer une opération de jointure sur des jeux de données résidant sur des clusters différents. Par exemple 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

Dans l’exemple ci-dessus, l’opération de jointure est une jointure entre clusters supposant que le cluster actuel n’est ni « SomeCluster » ni « SomeCluster2 ».

Notez que dans l’exemple suivant

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

l’opération de jointure n’est pas une jointure entre clusters, car ses opérandes proviennent du même cluster.

Quand Kusto rencontre une jointure entre plusieurs clusters, il détermine automatiquement l’emplacement d’exécution de l’opération de jointure. Cette décision peut avoir l’un des trois résultats possibles :
* Opération d’exécution de jointure sur le cluster de l’opérande de gauche, l’opérande de droite sera d’abord récupéré par ce cluster. (l’exemple de jointure **(1)** sera exécuté sur le cluster local)
* Opération d’exécution de jointure sur le cluster de l’opérande de droite, l’opérande de gauche sera d’abord récupéré par ce cluster. (l’exemple de jointure **(2)** sera exécuté sur « SomeCluster2 »)
* Exécuter une opération de jointure localement (c’est-à-dire sur le cluster qui a reçu la requête), les deux opérandes seront d’abord récupérés par le cluster local

La décision réelle dépend de la requête spécifique, la stratégie de communication à distance de jointure automatique est (version simplifiée) : «si l’un des opérandes est local, Join sera exécuté localement. Si les deux opérandes sont distants, la jointure à distance est exécutée sur le cluster de l’opérande de droite».

Parfois, les performances de la requête peuvent être considérablement améliorées si la stratégie de communication à distance automatique n’est pas respectée. En règle générale, il est préférable (du point de vue des performances) d’exécuter l’opération de jointure sur le cluster de l’opérande le plus grand.

Si, par exemple **(1)** le jeu de ```T | ...``` données informatique produit par est plus petit ```cluster("SomeCluster").database("SomeDB").T2 | ...``` que celui produit par, il est plus efficace d’exécuter l’opération de jointure sur « SomeCluster ».

Pour ce faire, vous pouvez donner à Kusto Join l’indicateur de communication à distance. La syntaxe est :

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

Voici les valeurs autorisées pour*`strategy`*
* **`left`**-exécuter Join sur le cluster de l’opérande gauche 
* **`right`**-exécuter Join sur le cluster de l’opérande de droite
* **`local`**-exécuter Join sur le cluster du cluster actif
* **`auto`**-(par défaut) laisser Kusto prendre la décision de communication à distance automatique

**Remarque :** L’indicateur de communication à distance Join sera ignoré par Kusto si la stratégie avec indicateur n’est pas applicable à l’opération de jointure.

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
