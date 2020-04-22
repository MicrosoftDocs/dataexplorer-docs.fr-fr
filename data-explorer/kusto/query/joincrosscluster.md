---
title: Joint Cross-Cluster - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Cross-Cluster Join in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1199b148fa295ac17417bbf590a73bfc9400a710
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765937"
---
# <a name="cross-cluster-join"></a>Cross-Cluster Join

::: zone pivot="azuredataexplorer"

Pour une discussion générale sur les requêtes à grappes croisées, consultez des [requêtes interfonds ou recoupées](cross-cluster-or-database-queries.md)

Il est possible d’effectuer des opérations de jointure sur des ensembles de données résidant sur différents clusters. Par exemple 

```kusto
T | ... | join (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1 // (1)

cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster2").database("SomeDB2").T2 | ...) on Col1 // (2)
```

Dans les exemples ci-dessus, une jointure transversale suppose que le cluster actuel n’est ni " SomeCluster " ni "SomeCluster2".

Notez que dans l’exemple suivant

```kusto
cluster("SomeCluster").database("SomeDB").T | ... | join (cluster("SomeCluster").database("SomeDB2").T2 | ...) on Col1 
```

l’opération de jointure n’est pas une jointure croisée parce que ses deux opérands proviennent du même cluster.

Lorsque Kusto rencontre la jointure en cluster transversal, il décidera automatiquement où exécuter l’opération de jointure elle-même. Cette décision peut avoir l’un des trois résultats possibles :
* Exécuter l’opération de jointure sur le cluster de l’opéra de gauche, opérand droit sera d’abord récupéré par ce cluster. (joindre l’exemple **(1)** sera exécuté sur le cluster local)
* Exécuter l’opération de jointure sur le cluster de l’opéra de droite, opérand gauche sera d’abord récupéré par ce cluster. (joindre l’exemple **(2)** sera exécuté sur le "SomeCluster2")
* Exécuter l’opération de jointure localement (c’est-à-dire sur le cluster qui a reçu la requête), les deux opérands seront d’abord récupérés par le cluster local

La décision réelle dépend de la requête spécifique, la stratégie de remotage automatique de jointure est (version simplifiée) : « Si l’un des opérands est local, la jointure sera exécutée localement. Si les deux opérandes sont à distance rejoindre sera exécuté sur le cluster de l’opéra droit".

Parfois, les performances de la requête peuvent être considérablement améliorées si la stratégie de remotage automatique n’est pas suivie. D’une manière générale, il est préférable (du point de vue de la performance) d’exécuter l’opération de jointure sur le cluster du plus grand opérande.

Si dans l’exemple **(1)** il jeu de données produit par ```T | ...``` est beaucoup plus petit que celui produit par ```cluster("SomeCluster").database("SomeDB").T2 | ...``` il est plus efficace d’exécuter l’opération de jointure sur "SomeCluster".

Ceci peut être réalisé en donnant Kusto rejoindre l’indice de remoting. La syntaxe est :

```kusto
T | ... | join hint.remote=<strategy> (cluster("SomeCluster").database("SomeDB").T2 | ...) on Col1
```

Voici les valeurs juridiques pour*`strategy`*
* **`left`**- exécuter la jointure sur le cluster de l’opérat gauche 
* **`right`**- exécuter la jointure sur le cluster de l’opéra droit
* **`local`**- exécuter la jointure sur le cluster actuel
* **`auto`**- (par défaut) laissez Kusto prendre la décision de remotage automatique

**Note:** Join remoting indice sera ignoré par Kusto si la stratégie évoquée n’est pas applicable à l’opération de jointure.

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
