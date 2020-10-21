---
title: extent_id ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit extent_id () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: eb1984ba80b5d2940591428fea4b1f6c3982f9d9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246633"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

Retourne un identificateur unique qui identifie le partition de données (« Extent ») dans lequel réside l’enregistrement en cours.

L’application de cette fonction aux données calculées qui ne sont pas attachées à un partition de données retourne un GUID vide (tous les zéros).

## <a name="syntax"></a>Syntaxe

`extent_id()`

## <a name="returns"></a>Retours

Valeur de type `guid` qui identifie le partition de données de l’enregistrement actif, ou un GUID vide (tous les zéros).

## <a name="example"></a>Exemple

L’exemple suivant montre comment obtenir la liste de tous les partitions de données qui ont des enregistrements à partir d’une heure avec une valeur spécifique pour la colonne `ActivityId` . Il montre que certains opérateurs de requête (ici, l' `where` opérateur, ainsi `extend` que et `project` ) conservent les informations sur le partition de données qui héberge l’enregistrement.

```kusto
T
| where Timestamp > ago(1h)
| where ActivityId == 'dd0595d4-183e-494e-b88e-54c52fe90e5a'
| extend eid=extent_id()
| summarize by eid
```

::: zone-end

::: zone pivot="azuremonitor"

Cette fonctionnalité n’est pas prise en charge dans Azure Monitor

::: zone-end
