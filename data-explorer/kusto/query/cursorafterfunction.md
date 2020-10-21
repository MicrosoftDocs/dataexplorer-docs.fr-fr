---
title: cursor_after ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit cursor_after () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3b1a89ab84b62241058a24573c0362f7215f82c7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252478"
---
# <a name="cursor_after"></a>cursor_after()

::: zone pivot="azuredataexplorer"

Prédicat sur les enregistrements d’une table pour comparer leur temps d’ingestion à un curseur de base de données.

## <a name="syntax"></a>Syntaxe

`cursor_after``(` *RHS*`)`

## <a name="arguments"></a>Arguments

* *RHS*: un littéral de chaîne vide ou une valeur de curseur de base de données valide.

## <a name="returns"></a>Retours

Valeur scalaire de type `bool` qui indique si l’enregistrement a été reçu après le curseur de base de données *RHS* ( `true` ) ou non ( `false` ).

**Notes**

Pour plus d’informations sur les curseurs de base de données, consultez [curseurs de](../management/databasecursor.md) base de données.

Cette fonction ne peut être appelée que sur les enregistrements d’une table pour laquelle la [stratégie IngestionTime](../management/ingestiontimepolicy.md) est activée.

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
