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
ms.openlocfilehash: a76ef031a3fbcaa26f90fd2c2664920805b823b5
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780267"
---
# <a name="external-tables"></a>Tables externes

Une **table externe** est une entité de schéma Kusto qui fait référence à des données stockées en dehors de la base de données Azure Explorateur de données.

Comme pour les [tables](tables.md), une table externe a un schéma bien défini (une liste ordonnée de noms de colonnes et de paires de types de données). Contrairement aux tables, les données sont stockées et gérées en dehors du cluster. La plupart du temps, les données sont stockées dans un format standard, tel que CSV, parquet, Avro, et ne sont pas ingérées par Azure Explorateur de données.

Une **table externe** est créée une fois. Consultez les commandes suivantes pour la création d’une table externe :
* [Commandes de contrôle générales de table externe](../../management/externaltables.md)
* [Créer et modifier des tables SQL externes](../../management/external-sql-tables.md)
* [Créer et modifier des tables dans le stockage Azure ou Azure Data Lake](../../management/external-tables-azurestorage-azuredatalake.md)

Une **table externe** peut être référencée par son nom à l’aide de la fonction [external_table ()](../../query/externaltablefunction.md) . 

**Notes**

* Noms des tables externes :
   * Respecte la casse.
   * Impossible de chevaucher les noms de table Kusto.
   * Suivez les règles pour les [noms d’entité](./entity-names.md).
* La limite maximale de tables externes par base de données est 1 000.
* Kusto prend [en charge l’exportation et](../../management/data-export/export-data-to-an-external-table.md) l' [exportation continue](../../management/data-export/continuous-data-export.md) vers une table externe, et l' [interrogation des tables externes](../../../data-lake-query-data.md).
* La [purge des données](../../concepts/data-purge.md) n’est pas appliquée aux tables externes. Les enregistrements ne sont jamais supprimés des tables externes.
