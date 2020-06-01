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
ms.openlocfilehash: 2b03f4e0ef426b07f88d7c179caecca6e7dd3034
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84257822"
---
# <a name="external-tables"></a>Tables externes

Une **table externe** est une entité de schéma Kusto qui fait référence à des données stockées en dehors de la base de données Kusto.

Comme pour les [tables](tables.md), une table externe a un schéma bien défini (une liste ordonnée de noms de colonnes et de paires de types de données). Contrairement aux tables, les données sont stockées et gérées en dehors du cluster Kusto. La plupart du temps, les données sont stockées dans un format standard, tel que CSV, parquet, Avro, et ne sont pas ingérées par Kusto.

Une **table externe** est créée une seule fois (voir les [commandes de contrôle général de table externe](../../management/externaltables.md), [créer et modifier des tables SQL externes](../../management/external-sql-tables.md), [créer et modifier des tables dans le stockage Azure ou Azure Data Lake](../../management/external-tables-azurestorage-azuredatalake.md)) et peut être référencée par son nom à l’aide de la fonction [external_table ()](../../query/externaltablefunction.md) . 

**Remarques**

* Les noms de tables externes respectent la casse.
* Les noms de tables externes ne peuvent pas chevaucher les noms de table Kusto.
* Les noms de tables externes suivent les règles pour les [noms d’entité](./entity-names.md).
* La limite maximale de tables externes par base de données est 1 000.
* Kusto prend [en](../../management/data-export/export-data-to-an-external-table.md) charge l’exportation et l' [exportation continue](../../management/data-export/continuous-data-export.md) vers une table externe, ainsi que l' [interrogation des tables externes](../../../data-lake-query-data.md).
    * La [purge des données](../../concepts/data-purge.md) n’est pas appliquée sur les tables externes. Les enregistrements ne sont jamais supprimés des tables externes.
