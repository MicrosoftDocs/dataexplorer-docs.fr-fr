---
title: cursor_current (), current_cursor ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit cursor_current (), current_cursor () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 12/10/2019
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 1b2e6f868d5117cbdd3db6ba88c6b77e5f0e8ccf
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252434"
---
# <a name="cursor_current-current_cursor"></a>cursor_current(), current_cursor()

::: zone pivot="azuredataexplorer"

Récupère la valeur actuelle du curseur de la base de données dans l’étendue. (Les noms `cursor_current` et `current_cursor` sont des synonymes.)

## <a name="syntax"></a>Syntaxe

`cursor_current()`

## <a name="returns"></a>Retours

Retourne une valeur unique de type `string` qui encode la valeur actuelle du curseur de la base de données dans l’étendue.

**Notes**

Pour plus d’informations sur les curseurs de base de données, consultez [curseurs de](../management/databasecursor.md) base de données.

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
