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
ms.openlocfilehash: 7f74732ed38d0b41a857fc549f549ce54ad4dce6
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863708"
---
# <a name="external-tables"></a>Tables externes

Une **table externe** est une entité de schéma Kusto qui fait référence à des données stockées en dehors de la base de données Kusto.

Comme pour les [tables](tables.md), une table externe a un schéma bien défini (une liste ordonnée de noms de colonnes et de paires de types de données). Contrairement aux tables, les données sont stockées et gérées en dehors du cluster Kusto. La plupart du temps, les données sont stockées dans un format standard, tel que CSV, parquet, Avro, et ne sont pas ingérées par Kusto.

Une **table externe** est créée une seule fois (voir les [commandes de contrôle général de table externe](../../management/externaltables.md), [créer et modifier des tables SQL externes](../../management/external-sql-tables.md)et [créer et modifier des tables dans le stockage](../../management/external-tables-azurestorage-azuredatalake.md)) et peut être référencée par son nom à l’aide de la fonction [external_table ()](../../query/externaltablefunction.md) . 

**Remarques**

* Les noms de tables externes respectent la casse.
* Les noms de tables externes ne peuvent pas chevaucher les noms de table Kusto.
* Les noms de tables externes suivent les règles pour les [noms d’entité](./entity-names.md).
* La limite maximale de tables externes par base de données est 1 000.
* Kusto prend en charge [l’exportation de données vers une table externe](../../management/data-export/export-data-to-an-external-table.md) , ainsi que l' [interrogation des tables externes](../../../data-lake-query-data.md).
