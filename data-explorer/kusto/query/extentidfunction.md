---
title: extent_id ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit extent_id () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 22f1b36b851c6e629abd2524feb4c40c74bbb1fa
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348137"
---
# <a name="extent_id"></a>extent_id()

::: zone pivot="azuredataexplorer"

Retourne un identificateur unique qui identifie le partition de données (« Extent ») dans lequel réside l’enregistrement en cours.

L’application de cette fonction aux données calculées qui ne sont pas attachées à un partition de données retourne un GUID vide (tous les zéros).

## <a name="syntax"></a>Syntaxe

`extent_id()`

## <a name="returns"></a>Retourne

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
