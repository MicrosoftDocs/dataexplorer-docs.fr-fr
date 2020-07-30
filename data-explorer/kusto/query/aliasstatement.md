---
title: Instruction d’alias-Azure Explorateur de données
description: Cet article décrit l’instruction alias dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 5e243984bd6a011b8de224d2c9cdd0108ab1b38f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349752"
---
# <a name="alias-statement"></a>Alias, instruction

::: zone pivot="azuredataexplorer"

Les instructions d’alias vous permettent de définir un alias pour les bases de données, qui peuvent être utilisées ultérieurement dans la même requête.

Cela est utile lorsque vous travaillez avec plusieurs clusters, mais que vous souhaitez qu’ils apparaissent comme si vous travailliez sur moins de clusters.
L’alias doit être défini conformément à la syntaxe suivante, où *ClusterName* et *DatabaseName* sont des entités existantes et valides.

## <a name="syntax"></a>Syntaxe

`alias`cluster de base de données [*« DatabaseAliasName »*] `=` (« https://*ClusterName*. Kusto. Windows. net : 443 »). Database («*DatabaseName*»)

`alias`cluster *DatabaseAliasName* de base de données `=` ("https://*ClusterName*. Kusto. Windows. net : 443"). Database ("*DatabaseName*")

* *'DatabaseAliasName'* peut être un nom existant ou un nouveau nom.
* L’URI du cluster mappé et le nom de la base de données mappée doivent figurer entre guillemets doubles (") ou guillemets simples (')

## <a name="examples"></a>Exemples

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

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
