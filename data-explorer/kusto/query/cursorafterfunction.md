---
title: cursor_after ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit cursor_after () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 9fab1ec936e950368667fc3afb133dcd952e44b5
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737689"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

Prédicat sur les enregistrements d’une table pour comparer leur temps d’ingestion à un curseur de base de données.

**Syntaxe**

`cursor_after``(` *RHS*`)`

**Arguments**

* *RHS*: un littéral de chaîne vide ou une valeur de curseur de base de données valide.

**Retourne**

Valeur scalaire de type `bool` qui indique si l’enregistrement a été reçu après le curseur de base de données`true` *RHS* () ou`false`non ().

**Remarques**

Pour plus d’informations sur les curseurs de base de données, consultez [curseurs de](../management/databasecursor.md) base de données.

Cette fonction ne peut être appelée que sur les enregistrements d’une table pour laquelle la [stratégie IngestionTime](../management/ingestiontimepolicy.md) est activée.

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
