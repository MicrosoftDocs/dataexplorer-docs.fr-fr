---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 12/22/2020
ms.author: orspodek
ms.openlocfilehash: 52a6de4c8ecb3ccbe4e315145b8ae8a3c314ebfc
ms.sourcegitcommit: 600c3430c00eb62d52540fe08dab386a860193cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102472199"
---
> [!NOTE]
> * Les propriétés système sont prises en charge pour les formats `json` et tabulaires (`csv`, `tsv`, etc.), mais pas sur les données compressées. Quand vous utilisez un format non pris en charge, les données sont toujours ingérées, mais les propriétés sont ignorées.
> * Pour les données tabulaires, les propriétés système sont prises en charge uniquement pour les messages d’événements à enregistrement unique.
> * Pour les données JSON, les propriétés système sont également prises en charge pour les messages d’événements à enregistrements multiples. Dans ce cas, les propriétés système sont ajoutées uniquement au premier enregistrement du message d’événement. 
> * Pour un mappage `csv`, des propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans la table [Propriétés système](../ingest-data-event-hub-overview.md#system-properties).
> * Pour un mappage `json`, des propriétés sont ajoutées en fonction des noms de propriété dans la table [Propriétés système](../ingest-data-event-hub-overview.md#system-properties).
