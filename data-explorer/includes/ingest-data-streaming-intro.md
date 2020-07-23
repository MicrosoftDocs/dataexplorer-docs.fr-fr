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
ms.openlocfilehash: e6704592a5b0f1a984ea5651eb8073ac105fda3d
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423164"
---
Utilisez l’ingestion de streaming pour charger des données lorsque vous avez besoin d’une faible latence entre l’ingestion et l’interrogation. L’opération d’ingestion de streaming se termine en moins de 10 secondes, et permet à vos données d’être interrogées immédiatement une fois celle-ci terminée. Cette méthode d’ingestion est adaptée pour la réception d’un volume important de données (par exemple, des milliers d’enregistrements par seconde), réparties dans des milliers de tables. Chaque table reçoit un volume de données relativement faible, par exemple quelques enregistrements par seconde.

Utilisez l’ingestion en bloc à la place de l’ingestion de streaming quand la quantité de données ingérées dépasse 4 Go par heure et par table.

Pour plus d’informations sur les méthodes d’ingestion, consultez [Vue d’ensemble de l’ingestion des données](../ingest-data-overview.md).
