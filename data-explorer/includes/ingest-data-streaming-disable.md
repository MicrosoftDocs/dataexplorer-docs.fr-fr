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
ms.openlocfilehash: a748332c85839bac8466532ba3959810a6387834
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423198"
---
## <a name="disable-streaming-ingestion-on-your-cluster"></a>Désactiver l’ingestion de streaming sur votre cluster

> [!WARNING]
> La désactivation de l’ingestion de streaming peut prendre plusieurs heures.

Avant de désactiver l’ingestion de streaming dans votre cluster Azure Data Explorer, supprimez la [stratégie d’ingestion de streaming](../kusto/management/streamingingestionpolicy.md) pour toutes les tables et bases de données concernées. La suppression de la stratégie d’ingestion de streaming déclenche la réorganisation des données au sein de votre cluster Azure Data Explorer. Les données d’ingestion de streaming sont déplacées du stockage initial vers le stockage permanent dans la banque de colonnes (étendues ou partitions). Ce processus peut prendre de quelques secondes à quelques heures, selon la quantité de données qui se trouvent dans le stockage initial.
