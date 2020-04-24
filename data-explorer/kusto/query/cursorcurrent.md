---
title: cursor_current (), current_cursor ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit cursor_current (), current_cursor () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: d9a9526fd846523510e8555c04ff3345d9008348
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030461"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor()

::: zone pivot="azuredataexplorer"

Récupère la valeur actuelle du curseur de la base de données dans l’étendue. (Les noms `cursor_current` et `current_cursor` sont des synonymes.)

**Syntaxe**

`cursor_current()`

**Retourne**

Retourne une valeur unique de type `string` qui encode la valeur actuelle du curseur de la base de données dans l’étendue.

**Remarques**

Pour plus d’informations sur les curseurs de base de données, consultez [curseurs de](../management/databasecursor.md) base de données.

::: zone-end

::: zone pivot="azuremonitor"

Cela n’est pas pris en charge dans Azure Monitor

::: zone-end
