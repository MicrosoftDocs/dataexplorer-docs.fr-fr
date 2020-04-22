---
title: Communiqué - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la déclaration de set dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 8cb9c1d72f1b2e8e4bfbbd28d67c04295c9ccf5b
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765574"
---
# <a name="set-statement"></a>Set, instruction

::: zone pivot="azuredataexplorer"

L’instruction `set` est utilisée pour définir une option de requête pour la durée de la requête.
Les options de requête contrôlent la manière dont une requête s’exécute et retourne les résultats. Ils peuvent être des drapeaux Boolean (hors par défaut), ou ont une valeur integer. Une requête peut contenir zéro, une ou plusieurs instructions set. Les énoncés définis n’affectent que les énoncés d’expression tabulaires qui les suivent dans l’ordre de programme.

* Les options de requête peuvent également être activées de manière programmatique en les plaçant dans l’objet. `ClientRequestProperties` Voir [ici](../api/netfx/request-properties.md).
  
* Les options de requête ne font pas officiellement partie de la langue Kusto et peuvent être modifiées sans être considérées comme un changement de langue de rupture.

**Syntaxe**

`set`*OptionName* `=` [ *OptionValue*]

**Exemple**

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
