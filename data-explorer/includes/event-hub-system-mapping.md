---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 12/22/2020
ms.author: orspodek
ms.openlocfilehash: bd12845234ec57c1393935b7366b8d2e387b3890
ms.sourcegitcommit: d19b4214625eeb1ec7aec4fd6c92007a07c76ebc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/22/2020
ms.locfileid: "99554819"
---
> [!NOTE]
> * Les propriétés système ne sont pas prises en charge sur les données compressées.
> * Pour les données tabulaires, les propriétés système sont prises en charge uniquement pour les messages d’événements à enregistrement unique.
> * Pour les données JSON, les propriétés système sont également prises en charge pour les messages d’événements à enregistrements multiples. Dans ce cas, les propriétés système sont ajoutées uniquement au premier enregistrement du message d’événement. 
> * Pour un mappage `csv`, des propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans la table [Propriétés système](../ingest-data-event-hub-overview.md#system-properties).
> * Pour un mappage `json`, des propriétés sont ajoutées en fonction des noms de propriété dans la table [Propriétés système](../ingest-data-event-hub-overview.md#system-properties).
