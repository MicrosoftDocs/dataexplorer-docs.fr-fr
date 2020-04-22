---
title: cursor_before_or_at() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit cursor_before_or_at() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 4d1752c69a6f3515b94c4050cef8f518ff58a235
ms.sourcegitcommit: 01eb9aaf1df2ebd5002eb7ea7367a9ef85dc4f5d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81765973"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

Un prédicat sur les enregistrements d’un tableau pour comparer leur temps d’ingestion avec un curseur de base de données.

**Syntaxe**

`cursor_before_or_at``(` *RHS (EN)*`)`

**Arguments**

* *RHS*: Soit une chaîne vide littérale, soit une valeur valide de curseur de base de données.

**Retourne**

Une valeur scalaire `bool` de type qui indique si l’enregistrement a été ingéré avant`false`ou à la base de données curseur *RHS* (`true`) ou non ( ).

**Remarques**

Consultez [les curseurs de base de données](../management/databasecursor.md) pour plus de détails sur les curseurs de base de données.

Cette fonction ne peut être invoquée que sur les dossiers d’une table qui a activé la [politique IngestionTime.](../management/ingestiontimepolicy.md)

::: zone-end

::: zone pivot="azuremonitor"

Ce n’est pas pris en charge dans Azure Monitor

::: zone-end
