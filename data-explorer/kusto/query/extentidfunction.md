---
title: extent_id ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit extent_id () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 300f7961fd11b433ef4e420d5a20b9ad9150b269
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737621"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

Retourne un identificateur unique qui identifie le partition de données (« Extent ») dans lequel réside l’enregistrement en cours. 

L’application de cette fonction aux données calculées qui ne sont pas attachées à un partition de données retourne un GUID vide (tous les zéros).

**Syntaxe**

`extent_id()`

**Retourne**

Valeur de type `guid` qui identifie le partition de données de l’enregistrement actif, ou un GUID vide (tous les zéros).

**Exemple**

L’exemple suivant montre comment obtenir la liste de tous les partitions de données qui ont des enregistrements à partir d’une heure avec une valeur spécifique pour `ActivityId`la colonne. Il montre que certains opérateurs de requête (ici, `where` l’opérateur, mais cela est également vrai `extend` pour `project`et) conservent les informations sur le partition de données qui héberge l’enregistrement.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend eid=extent_id()
| summarize by eid
```

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
