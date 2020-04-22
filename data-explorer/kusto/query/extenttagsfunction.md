---
title: extent_tags() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit extent_tags() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d8af7e51c5e2efb16763541db05e9ccc7e2cb95f
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765428"
---
# <a name="extent_tags"></a>extent_tags()

::: zone pivot="azuredataexplorer"

Retourne un tableau dynamique avec les [balises](../management/extents-overview.md#extent-tagging) de l’éclat de données («étendue») dans lequel se trouve l’enregistrement actuel. 

L’application de cette fonction à des données calculées qui ne sont pas attachées à un éclat de données renvoie une valeur vide.

**Syntaxe**

`extent_tags()`

**Retourne**

Une valeur `dynamic` de type qui est un tableau contenant les étiquettes d’étendue de l’enregistrement actuel, ou une valeur vide.

**Exemples**

L’exemple suivant montre comment obtenir une liste les balises de tous les éclats de `ActivityId`données qui ont des enregistrements d’il ya une heure avec une valeur spécifique pour la colonne . Il démontre que certains opérateurs de `where` requête (ici, l’opérateur, mais cela est également vrai pour `extend` et `project`) préserver les informations sur les données éclatant hébergeant le dossier.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend tags = extent_tags()
| summarize by tostring(tags)
```

L’exemple suivant montre comment obtenir un compte de tous les enregistrements de la dernière `MyTag` heure, qui sont stockés dans des `drop-by:MyOtherTag`étendues qui sont étiquetées avec l’étiquette (et potentiellement d’autres balises), mais pas étiqueté avec l’étiquette .

```kusto
T
| where Timestamp > ago(1h)
| extend Tags = extent_tags()
| where Tags has_cs 'MyTag' and Tags !has_cs 'drop-by:MyOtherTag'
| count
```

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
