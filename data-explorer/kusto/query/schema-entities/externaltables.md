---
title: Tables externes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit des tableaux externes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/22/2020
ms.openlocfilehash: f4a059ba4533ed91353e75b499e564e6dd06d591
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509382"
---
# <a name="external-tables"></a>Tables externes

Une **table externe** est une entité de schéma Kusto qui fait référence aux données stockées en dehors de la base de données Kusto.

Semblable aux [tableaux,](tables.md)une table externe a un schéma bien défini (une liste ordonnée de noms de colonne et de paires de type de données). Contrairement aux tableaux, les données sont stockées et gérées en dehors du cluster Kusto. Le plus souvent, les données sont stockées dans un format standard comme CSV, Parquet, Avro, et n’est pas ingérée par Kusto.

Une **table externe** est créée une fois (voir commandes de contrôle de la Table [externe)](../../management/externaltables.md)et peut être référencée par son nom à [l’aide de external_table)fonction.](../../query/externaltablefunction.md) 

**Remarques**

* Les noms de table externes sont sensibles aux cas.
* Les noms de table externes ne peuvent pas se chevaucher avec les noms de table Kusto.
* Les noms de table externes suivent les règles pour les [noms d’entités](./entity-names.md).
* La limite maximale des tables externes par base de données est de 1 000.
* Kusto prend en charge [l’exportation de données vers une table externe](../../management/data-export/export-data-to-an-external-table.md) ainsi que la requête en tables [externes](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data).
