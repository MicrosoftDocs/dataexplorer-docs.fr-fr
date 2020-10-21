---
title: extent_tags ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit extent_tags () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: da705d558a09bdcc52bf07fc807e53fdccb9396c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249089"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

Retourne un tableau dynamique avec les [balises](../management/extents-overview.md#extent-tagging) du partition de données (« Extent ») dans lequel réside l’enregistrement en cours. 

L’application de cette fonction aux données calculées qui ne sont pas attachées à un partition de données retourne une valeur vide.

## <a name="syntax"></a>Syntaxe

`extent_tags()`

## <a name="returns"></a>Retours

Valeur de type `dynamic` qui est un tableau contenant les balises d’étendue de l’enregistrement actuel ou une valeur vide.

## <a name="examples"></a>Exemples

L’exemple suivant montre comment obtenir la liste des balises de tous les partitions de données qui ont des enregistrements d’une heure auparavant, avec une valeur spécifique pour la colonne `ActivityId` . Il montre que certains opérateurs de requête (ici, l' `where` opérateur, mais cela est également vrai pour `extend` et `project` ) conservent les informations sur le partition de données qui héberge l’enregistrement.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

L’exemple suivant montre comment obtenir le décompte de tous les enregistrements de la dernière heure, qui sont stockés dans des étendues qui sont balisées avec la balise `MyTag` (et éventuellement d’autres balises), mais qui ne sont pas marquées avec la balise `drop-by:MyOtherTag` .

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
