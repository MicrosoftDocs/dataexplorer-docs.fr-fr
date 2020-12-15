---
title: Ingérer à partir d’IoT Hub - Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’ingestion à partir d’IoT Hub dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: b76321fd843efe915a6fd55797bd2dc68059b004
ms.sourcegitcommit: 8ac4717dbff679991b122b09a0c1ed700562a736
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97488574"
---
# <a name="iot-hub-data-connection"></a>Connexion de données IoT Hub

[Azure IoT Hub](/azure/iot-hub/about-iot-hub) est un service managé, hébergé dans le cloud, qui fait office de hub de messages central pour la communication bidirectionnelle entre votre application IoT et les appareils qu’il gère. Azure Data Explorer propose une ingestion continue à partir des hubs IoT gérés par le client, à l’aide de son [point de terminaison intégré compatible avec Event Hub](/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints).

Le pipeline d’ingestion IoT passe par plusieurs étapes. Tout d’abord, vous créez un hub IoT et y inscrivez un appareil. Vous créez ensuite une table cible dans Azure Data Explorer, dans laquelle les [données d’un format particulier](#data-format) sont ingérées à l’aide des [propriétés d’ingestion](#ingestion-properties) indiquées. La connexion Iot Hub doit connaître le [routage des événements](#events-routing) pour se connecter à la table Azure Data Explorer. Les données sont incorporées avec les propriétés sélectionnées en fonction du [mappage des propriétés du système d’événements](#event-system-properties-mapping). Ce processus peut être géré par le biais du [portail Azure](ingest-data-iot-hub.md), programmatiquement avec [C#](data-connection-iot-hub-csharp.md) ou [Python](data-connection-iot-hub-python.md), ou avec le [modèle Azure Resource Manager](data-connection-iot-hub-resource-manager.md).

Pour obtenir des informations générales sur l’ingestion de données dans Azure Data Explorer, consultez [Vue d’ensemble de l’ingestion des données dans Azure Data Explorer](ingest-data-overview.md).

## <a name="data-format"></a>Format de données

* Les données sont lues à partir du point de terminaison Event Hub sous forme d’objets [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet).
* Examinez les [formats pris en charge](ingestion-supported-formats.md).
    > [!NOTE]
    > IoT Hub ne prend pas en charge le format .raw.
* Examinez les [compressions prises en charge](ingestion-supported-formats.md#supported-data-compression-formats).
  * La taille des données non compressées d’origine doit faire partie des métadonnées d’objets blob, sinon Azure Data Explorer l’estime. La limite de taille décompressée d’ingestion par fichier est de 4 Go.

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Les propriétés d’ingestion déterminent le processus d’ingestion où router les données et comment le traiter. Vous pouvez spécifier les [propriétés d’ingestion](ingestion-properties.md) des événements avec [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Vous pouvez définir les propriétés suivantes :

|Propriété |Description|
|---|---|
| Table de charge de travail | Nom (sensible à la casse) de la table cible existante. Remplace le paramètre `Table` défini dans le volet `Data Connection`. |
| Format | Format de données. Remplace le paramètre `Data format` défini dans le volet `Data Connection`. |
| IngestionMappingReference | Nom du [mappage d’ingestion](kusto/management/create-ingestion-mapping-command.md) existant à utiliser. Remplace le paramètre `Column mapping` défini dans le volet `Data Connection`.|
| Encodage |  Encodage des données, la valeur par défaut est UTF8. Il peut s’agir de l’un des [encodages pris en charge par .NET](/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |

> [!NOTE]
> Seuls les événements mis en file d’attente après que vous avez créé la connexion de données sont ingérés.

## <a name="events-routing"></a>Routage d’événements

Lors de la configuration d’une connexion Iot Hub au cluster Azure Data Explorer, vous spécifiez les propriétés de la table cible (nom de table, format de données et mappage). Ce paramétrage est le routage par défaut de vos données, également appelé routage statique.
Vous pouvez également spécifier des propriétés de la table cible pour chaque événement, à l’aide des propriétés d’événement. La connexion route dynamiquement les données comme spécifié dans [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), en remplaçant les propriétés statiques de cet événement.

> [!Note]
> Si l’option **Mes données contiennent des informations de routage** est sélectionnée, vous devez fournir les informations de routage nécessaires dans le cadre des propriétés des événements.

## <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

Les propriétés système sont une collection utilisée pour stocker les propriétés définies par le service IoT Hub, au moment de la réception de l’événement. La connexion Iot Hub d’Azure Data Explorer incorpore les propriétés sélectionnées dans les données qui arrivent dans votre table.

> [!Note]
> Pour un mappage `csv`, des propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessous. Pour un mappage `json`, des propriétés sont ajoutées en fonction des noms de propriété dans le tableau suivant.

### <a name="system-properties"></a>Propriétés système

IoT Hub expose les propriétés système suivantes :

|Propriété |Description|
|---|---|
| message-id | Identificateur correspondant au message défini par l’utilisateur utilisé pour les modèles demande-réponse. |
| sequence-number | Un numéro (unique par file d’attente d’appareil) affecté par IoT Hub à chaque message cloud-à-appareil. |
| par | Une destination spécifiée dans les messages cloud vers appareil . |
| absolute-expiry-time | Date et heure d’expiration du message. |
| iothub-enqueuedtime | Date et heure de réception du message Appareil-à-cloud par IoT Hub. |
| correlation-id| Une propriété de chaîne d’un message de réponse qui contient généralement l'ID du message de la demande dans les modèles demande-réponse. |
| user-id| Un ID utilisé pour spécifier l’origine des messages. |
| iothub-ack| Un générateur de messages de commentaires. |
| iothub-connection-device-id| Un ID défini par IoT Hub sur les messages appareil vers cloud. Elle contient la propriété deviceId de l’appareil qui a envoyé le message. |
| iothub-connection-auth-generation-id| Un ID défini par IoT Hub sur les messages appareil vers cloud. Il contient la propriété connectionDeviceGenerationId (conformément aux Propriétés d’identité des appareils) de l’appareil qui a envoyé le message. |
| iothub-connection-auth-method| Une méthode d’authentification définie par IoT Hub sur les messages appareil-à-cloud. Cette propriété contient des informations sur la méthode d’authentification utilisée pour authentifier l’appareil qui a envoyé le message. |

Si vous avez sélectionné **Propriétés du système d’événements** dans la section **Source de données** de la table, vous devez inclure les propriétés dans le schéma et le mappage de table.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="iot-hub-connection"></a>Connexion au hub IoT

> [!Note]
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Data Explorer.

### <a name="create-an-iot-hub"></a>Création d’un IoT Hub

[Créez un hub IoT](ingest-data-iot-hub.md#create-an-iot-hub) si vous n’en avez pas déjà un. La connexion à IoT Hub peut être gérée par le biais du [portail Azure](ingest-data-iot-hub.md), programmatiquement avec [C#](data-connection-iot-hub-csharp.md) ou [Python](data-connection-iot-hub-python.md), ou avec le [modèle Azure Resource Manager](data-connection-iot-hub-resource-manager.md).

> [!Note]
> * Le nombre de `device-to-cloud partitions` n’étant pas modifiable, définissez-le dans une perspective à long terme.
> * Le groupe de consommateurs doit être unique par consommateur. Créez un groupe de consommateurs dédié à la connexion Azure Data Explorer. Recherchez votre ressource dans le portail Azure et accédez à `Built-in endpoints` pour ajouter un nouveau groupe de consommateurs.

## <a name="sending-events"></a>Envoi des événements

Consultez l’[exemple de projet](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/SimulatedDevice) qui simule un appareil et génère des données.

## <a name="next-steps"></a>Étapes suivantes

Il existe différentes méthodes pour ingérer des données dans IoT Hub. Consultez les liens suivants pour obtenir des procédures pas à pas de chaque méthode.

* [Ingérer les données d’un hub IoT dans Azure Data Explorer](ingest-data-iot-hub.md)
* [Créer une connexion de données au hub IoT pour Azure Data Explorer à l’aide de C# (préversion)](data-connection-iot-hub-csharp.md)
* [Créer une connexion de données au hub IoT pour Azure Data Explorer à l’aide de Python (préversion)](data-connection-iot-hub-python.md)
* [Créer une connexion de données IoT Hub pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager](data-connection-iot-hub-resource-manager.md)
