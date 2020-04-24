---
title: ingestion_time ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ingestion_time () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 2474b8751be5cba2270bcbd2936c76b91f5f3ba0
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82030036"
---
# <a name="ingestion_time"></a>ingestion_time()

Récupère la colonne masquée `$IngestionTime` `datetime` de l’enregistrement, ou null.
La `$IngestionTime` colonne est définie automatiquement lorsque la table

::: zone pivot="azuredataexplorer"

La [stratégie IngestionTime](../management/ingestiontimepolicy.md) est définie (activée).

::: zone-end

::: zone pivot="azuremonitor"

La stratégie IngestionTime est définie (activée).

::: zone-end

Si cette stratégie n’est pas définie pour la table, une valeur null est retournée.

Cette fonction doit être utilisée dans le contexte d’une table réelle pour retourner les données pertinentes. Par exemple, elle retourne la valeur null pour tous les enregistrements si elle est appelée à `summarize` la suite d’un opérateur.

**Syntaxe**

 `ingestion_time()`

**Retourne**

`datetime` Valeur qui spécifie le temps approximatif d’ingestion d’une table.

**Exemple**

```kusto
T 
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
