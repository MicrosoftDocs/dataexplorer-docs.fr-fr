---
title: Fichier include
description: Fichier include
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 07/13/2020
ms.author: orspodek
ms.reviewer: alexefro
ms.custom: include file
ms.openlocfilehash: 33168f001dd3d998fbf82169c7637e2eb390e0bc
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423117"
---
## <a name="limitations"></a>Limites

* Les [curseurs de base de données](../kusto/management/databasecursor.md) ne sont pas pris en charge pour une base de données si une [stratégie d’ingestion de streaming](../kusto/management/streamingingestionpolicy.md) est définie et activée pour la base de données ou l’une de ses tables.
* Les [mappages de données](../kusto/management/mappings.md) doivent être [précréés](../kusto/management/create-ingestion-mapping-command.md) pour être utilisés dans une ingestion de streaming. Les demandes d’ingestion en streaming individuelles ne prennent pas en charge les mappages de données inline.
* La capacité et les performances d’ingestion de streaming évoluent avec l’augmentation des tailles de cluster et de machine virtuelle. Le nombre de demandes d’ingestion simultanées est limité à six par cœur. Par exemple, pour les références SKU 16 cœurs telles que D14 et L16, la charge maximale prise en charge est de 96 demandes d’ingestion simultanées. Pour les références SKU 2 cœurs telles que D11, la charge maximale prise en charge est de 12 demandes d’ingestion simultanées.
* La taille limite des données par demande d’ingestion en streaming est de 4 Mo.
* Les mises à jour de schéma, telles que la création et la modification de tables et de mappages d’ingestion, peuvent prendre jusqu’à cinq minutes pour le service d’ingestion de streaming. Pour plus d’informations, consultez [Ingestion de streaming et changements de schéma](../kusto/management/data-ingestion/streaming-ingestion-schema-changes.md).
* L’activation de l’ingestion de streaming sur un cluster, même lorsque les données ne sont pas ingérées via streaming, utilise une partie du disque SSD local des machines du cluster pour les données d’ingestion de streaming et réduit le stockage disponible pour le cache à chaud.
* Les [étiquettes d’étendue](../kusto/management/extents-overview.md#extent-tagging) ne peuvent pas être définies dans les données d’ingestion de streaming.
* Si l’ingestion de streaming est utilisée sur l’une des tables de la base de données, la base de données ne pourra pas être utilisée en tant que leader pour les [bases de données abonnées](../follower.md), ni en tant que [fournisseur de données](../data-share.md#data-provider---share-data) pour Azure Data Share.
