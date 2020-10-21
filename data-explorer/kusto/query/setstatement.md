---
title: Instruction Set-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’instruction Set dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: b8b30aafd8fafc2a900fe0596243a0d4ed89f276
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252064"
---
# <a name="set-statement"></a>Set, instruction

::: zone pivot="azuredataexplorer"

L' `set` instruction permet de définir une option de requête pour la durée de la requête.
Les options de requête contrôlent la manière dont une requête s’exécute et retourne les résultats. Ils peuvent être des indicateurs booléens (désactivés par défaut) ou avoir une valeur entière. Une requête peut contenir zéro, une ou plusieurs instructions set. Les instructions SET affectent uniquement les instructions d’expression tabulaire qui les tracent dans l’ordre du programme.

* Les options de requête peuvent également être activées par programme en les définissant dans l' `ClientRequestProperties` objet. Voir [ici](../api/netfx/request-properties.md).
  
* Les options de requête ne font pas partie intégrante du langage Kusto et peuvent être modifiées sans être considérées comme un changement de langue.

## <a name="syntax"></a>Syntaxe

`set`*NomOption* [ `=` *OptionValue*]

## <a name="example"></a>Exemple

```kusto
set querytrace;
Events | take 100
```

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
