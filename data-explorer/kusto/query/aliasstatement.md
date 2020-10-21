---
title: Instruction d’alias-Azure Explorateur de données
description: Cet article décrit l’instruction alias dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 822c8eccf50dc30fd3f56f4402c10a9fafb34084
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248292"
---
# <a name="alias-statement"></a>Alias, instruction

::: zone pivot="azuredataexplorer"

Les instructions d’alias vous permettent de définir un alias pour les bases de données, qui peuvent être utilisées ultérieurement dans la même requête.

Cela est utile lorsque vous travaillez avec plusieurs clusters, mais que vous souhaitez qu’ils apparaissent comme si vous travailliez sur moins de clusters.
L’alias doit être défini conformément à la syntaxe suivante, où *ClusterName* et *DatabaseName* sont des entités existantes et valides.

## <a name="syntax"></a>Syntaxe

`alias` cluster de base de données [*« DatabaseAliasName »*] `=` (« https://*ClusterName*. Kusto. Windows. net : 443 »). Database («*DatabaseName*»)

`alias` cluster *DatabaseAliasName* de base de données `=` ("https://*ClusterName*. Kusto. Windows. net : 443"). Database ("*DatabaseName*")

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
