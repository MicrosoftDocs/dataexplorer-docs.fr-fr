---
title: cursor_before_or_at ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit cursor_before_or_at () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: c053cd307f8cff8ad00eff0a4224ebbea2808c6c
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737672"
---
# <a name="cursor_before_or_at"></a>cursor_before_or_at()

::: zone pivot="azuredataexplorer"

Prédicat sur les enregistrements d’une table pour comparer leur temps d’ingestion à un curseur de base de données.

**Syntaxe**

`cursor_before_or_at``(` *RHS*`)`

**Arguments**

* *RHS*: un littéral de chaîne vide ou une valeur de curseur de base de données valide.

**Retourne**

Valeur `bool` scalaire de type qui indique si l’enregistrement a été ingéré avant ou au niveau du curseur de base de données *RHS* (`false``true`) ou non ().

**Remarques**

Pour plus d’informations sur les curseurs de base de données, consultez [curseurs de](../management/databasecursor.md) base de données.

Cette fonction ne peut être appelée que sur les enregistrements d’une table pour laquelle la [stratégie IngestionTime](../management/ingestiontimepolicy.md) est activée.

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
