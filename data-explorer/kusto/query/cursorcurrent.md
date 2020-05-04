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
ms.openlocfilehash: 5291fa664dc4736179d7f20984eacfd44efd5888
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737655"
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

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
