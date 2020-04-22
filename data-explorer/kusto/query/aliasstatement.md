---
title: Déclaration d’Alias - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la déclaration d’Alias dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c6c689ab6daacebe1cd20742b199c8b9cc299245
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81766092"
---
# <a name="alias-statement"></a>Alias, instruction

::: zone pivot="azuredataexplorer"

Les instructions Alias permettent de définir l’alias pour les bases de données qui peuvent être utilisées plus tard dans la même requête.

Ceci est utile lorsque vous travaillez avec plusieurs clusters tout en essayant d’apparaître comme travaillant sur moins de clusters ou seulement sur un seul cluster.
L’alias doit être défini en fonction de la syntaxe suivante où le *nom de clustername* et *le nom de base de données* doit être une entité existante et valide.

**Syntaxe**

`alias`base de données[*'DatabaseAliasName' ]* `=` cluster ("https://*clustername*.kusto.windows.net:443").database ("*databasename*")

`alias`base de données *DatabaseAliasName* `=` cluster ("https://*clustername*.kusto.windows.net:443").database ("*databasename*")

* *'DatabaseAliasName'* peut être soit en nom d’axe ou un nouveau nom.
* Le cluster-uri cartographié et le nom de base de données cartographié doivent apparaître à l’intérieur de double-citations(«) ou de citations simples(')

**Exemples**

```kusto
alias database["wiki"] = cluster("https://somecluster.kusto.windows.net:443").database("somedatabase");
database("wiki").PageViews | count 
```

```kusto
alias database Logs = cluster("https://othercluster.kusto.windows.net:443").database("otherdatabase");
database("Logs").Traces | count 
```

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
