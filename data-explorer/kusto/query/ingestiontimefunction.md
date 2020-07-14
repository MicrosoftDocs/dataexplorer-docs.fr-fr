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
ms.openlocfilehash: fea923b0d917beb505bd6a1cb9ee1339739d08c6
ms.sourcegitcommit: 284152eba9ee52e06d710cc13200a80e9cbd0a8b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86291557"
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

**Syntaxe**

`ingestion_time()`

**Retourne**

`datetime`Valeur qui spécifie le temps approximatif d’ingestion d’une table.

**Exemple**

```kusto
T
| extend ingestionTime = ingestion_time() | top 10 by ingestionTime
```
