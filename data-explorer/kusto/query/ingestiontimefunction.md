---
title: ingestion_time ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit ingestion_time () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: ba4429639f7c4775eab34797b7d7ff04fa247482
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252270"
---
# <a name="ingestion_time"></a>ingestion_time()

::: zone pivot="azuredataexplorer"

Retourne l’heure approximative à laquelle l’enregistrement actif a été reçu.

Cette fonction doit être utilisée dans le contexte d’une table de données ingérées pour laquelle la [stratégie IngestionTime](../management/ingestiontimepolicy.md) a été activée lorsque les données ont été ingérées. Dans le cas contraire, cette fonction produit des valeurs NULL.

::: zone-end

::: zone pivot="azuremonitor"

Récupère le `datetime` lorsque l’enregistrement a été ingéré et prêt pour la requête.

::: zone-end

> [!NOTE]
> La valeur retournée par cette fonction est uniquement approximative, car le processus d’ingestion peut prendre plusieurs minutes pour s’exécuter et plusieurs activités d’ingestion peuvent avoir lieu simultanément. Pour traiter tous les enregistrements d’une table avec des garanties exactement une fois, utilisez des [curseurs de base de données](../management/databasecursor.md).

## <a name="syntax"></a>Syntaxe

`ingestion_time()`

## <a name="returns"></a>Retours

`datetime`Valeur qui spécifie le temps approximatif d’ingestion d’une table.

## <a name="example"></a>Exemple

```kusto
T
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
