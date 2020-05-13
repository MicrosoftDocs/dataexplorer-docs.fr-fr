---
title: Tables externes-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les tables externes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: cfcf8a18bac1f6369b75538f2172fe8f25cb9660
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83372942"
---
# <a name="external-tables"></a>Tables externes

Une **table externe** est une entité de schéma Kusto qui fait référence à des données stockées en dehors de la base de données Kusto.

Comme pour les [tables](tables.md), une table externe a un schéma bien défini (une liste ordonnée de noms de colonnes et de paires de types de données). Contrairement aux tables, les données sont stockées et gérées en dehors du cluster Kusto. La plupart du temps, les données sont stockées dans un format standard, tel que CSV, parquet, Avro, et ne sont pas ingérées par Kusto.

Une **table externe** est créée une seule fois (voir les [commandes de contrôle de table externe](../../management/externaltables.md)) et peut être référencée par son nom à l’aide de la fonction [external_table ()](../../query/externaltablefunction.md) . 

**Remarques**

* Les noms de tables externes respectent la casse.
* Les noms de tables externes ne peuvent pas chevaucher les noms de table Kusto.
* Les noms de tables externes suivent les règles pour les [noms d’entité](./entity-names.md).
* La limite maximale de tables externes par base de données est 1 000.
* Kusto prend en charge [l’exportation de données vers une table externe](../../management/data-export/export-data-to-an-external-table.md) , ainsi que l' [interrogation des tables externes](../../../data-lake-query-data.md).
