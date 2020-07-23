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
ms.openlocfilehash: 1ee658d2c16bcf4174bb82d61ddc8c9b2d7d126f
ms.sourcegitcommit: 537a7eaf8c8e06a5bde57503fedd1c3706dd2b45
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2020
ms.locfileid: "86423140"
---
## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Utiliser l’ingestion de streaming pour ingérer des données dans votre cluster

Deux types d’ingestion de streaming sont pris en charge :

* [**Event Hub**](../ingest-data-event-hub.md) et [**IoT Hub**](../ingest-data-iot-hub.md), qui sont utilisés comme des sources de données.
* Une **ingestion personnalisée** vous demande d’écrire une application qui utilise l’une des [bibliothèques clientes](../kusto/api/client-libraries.md) Azure Data Explorer. Consultez un [exemple d’ingestion de streaming](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) pour voir un exemple d’application.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Choisir le type d’ingestion de streaming approprié

|Critère|Event Hub|Ingestion personnalisée|
|---------|---------|---------|
|Délai de données entre le lancement de l’ingestion et le moment où les données sont disponibles pour une requête | Délai plus long | Délai plus court  |
|Surcharge de développement | Installation rapide et facile, aucune surcharge de développement | Surcharge de développement élevée pour l’application afin de gérer les erreurs et d’assurer la cohérence des données |
