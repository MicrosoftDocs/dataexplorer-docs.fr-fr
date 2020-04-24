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
ms.openlocfilehash: 5ccb3c73bb51033fb55c60c35468a5909e87e104
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82029999"
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

Cela n’est pas pris en charge dans Azure Monitor

::: zone-end
