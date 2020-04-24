---
title: Ingest from IoT Hub - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Ingest de IoT Hub dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 33b6f4f737657ee0a6c2712f3b7cadce0c9c0a8f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521350"
---
# <a name="ingest-from-iot-hub"></a>Ingest de IoT Hub

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) est un service géré, hébergé dans le cloud, qui agit comme un centre de message central pour la communication bidirectionnelle entre votre application IoT et les appareils qu’il gère. Azure Data Explorer offre une ingestion continue à partir de hubs IoT gérés par le client, en utilisant son [compatible Event Hub intégré en point final](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints).

## <a name="data-format"></a>Format de données

* Les données sont lues à partir du point de terminaison Event Hub sous forme d’objets [EventData.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)
* La charge utile d’événement peut être dans l’un des [formats pris en charge par Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Les propriétés d’ingestion instruisent le processus d’ingestion. Où acheminer les données et comment les traiter. Vous pouvez spécifier les [propriétés Ingestion](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) de l’ingestion des événements à l’aide de [l’EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Vous pouvez définir les propriétés suivantes :

|Propriété |Description|
|---|---|
| Table de charge de travail | Nom (sensible au cas) de la table cible existante. Remplace l’ensemble `Table` sur `Data Connection` la lame. |
| Format | Format de données. Remplace l’ensemble `Data format` sur `Data Connection` la lame. |
| IngestionMappingReference | Nom de la [cartographie d’ingestion](../create-ingestion-mapping-command.md) existante à utiliser. Remplace l’ensemble `Column mapping` sur `Data Connection` la lame.|
| Encodage |  L’encodage des données, la valeur par défaut est UTF8. Peut être l’un des [encodages supportés .NET](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |

## <a name="events-routing"></a>Itinéraire d’événements

Lors de la configuration d’une connexion IoT Hub au cluster Azure Data Explorer, vous spécifiez les propriétés de la table cible (nom de table, format de données et cartographie). Il s’agit du routage par défaut `static routing`pour vos données, également appelé .
Vous pouvez également spécifier les propriétés de table cible pour chaque événement, en utilisant les propriétés de l’événement. La connexion permettra d’acheminer dynamiquement les données comme spécifié dans le [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), l’emporter sur les propriétés statiques pour cet événement.

## <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

Les propriétés du système sont une collection utilisée pour stocker les propriétés qui sont définies par le service IoT Hubs, au moment où l’événement est reçu. La connexion Azure Data Explorer IoT Hub intégrera les propriétés sélectionnées dans l’atterrissage de données dans votre table.

> [!Note]
> * Pour `csv` la cartographie, les propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessous. Pour `json` la cartographie, les propriétés sont ajoutées en fonction des noms de propriété dans le tableau suivant.

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Hub expose les propriétés système suivantes :

|Propriété |Description|
|---|---|
| message-id | Identificateur correspondant au message défini par l’utilisateur utilisé pour les modèles demande-réponse. |
| sequence-number | Un numéro (unique par file d’attente d’appareil) affecté par IoT Hub à chaque message cloud-à-appareil. |
| to | Une destination spécifiée dans les messages cloud vers appareil . |
| absolute-expiry-time | Date et heure d’expiration du message. |
| iothub-enqueuedtime | Date et heure de réception du message Appareil-à-cloud par IoT Hub. |
| correlation-id| Une propriété de chaîne d’un message de réponse qui contient généralement l'ID du message de la demande dans les modèles demande-réponse. |
| user-id| Un ID utilisé pour spécifier l’origine des messages. |
| iothub-ack| Un générateur de messages de commentaires. |
| iothub-connection-device-id| Un ID défini par IoT Hub sur les messages appareil vers cloud. Elle contient la propriété deviceId de l’appareil qui a envoyé le message. |
| iothub-connection-auth-generation-id| Un ID défini par IoT Hub sur les messages appareil vers cloud. Il contient la propriété connectionDeviceGenerationId (conformément aux Propriétés d’identité des appareils) de l’appareil qui a envoyé le message. |
| iothub-connection-auth-method| Une méthode d’authentification définie par IoT Hub sur les messages appareil-à-cloud. Cette propriété contient des informations sur la méthode d’authentification utilisée pour authentifier l’appareil qui a envoyé le message. |

Si vous avez sélectionné **les propriétés du système Event** dans la section Source de **données** de la table, vous devez inclure les propriétés dans le schéma de table et la cartographie.

**Exemple de schéma de table**

Si vos données comprennent`Timespan` `Metric`trois `Value`colonnes ( , `x-opt-enqueued-time` , `x-opt-offset`et ) et les propriétés que vous incluez sont et, créer ou modifier le schéma de table en utilisant cette commande:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**Exemple de cartographie CSV**

Exécutez les commandes suivantes pour ajouter des données au début de l’enregistrement. Valeurs ordinaires de note : les propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessus. Ceci est important pour la cartographie CSV où lesordinaux de colonne changeront en fonction des propriétés du système qui sont cartographiées.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Exemple de cartographie JSON**

Les données sont ajoutées en utilisant les noms des propriétés du système tels qu’ils apparaissent dans la liste des propriétés du **système d’événements** de lame de **connexion données.** Exécutez ces commandes :

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```

## <a name="create-iot-hub-connection"></a>Créer la connexion IoT Hub

> [!Note]
> Pour de meilleures performances, créez toutes les ressources dans la même région que le cluster Azure Data Explorer.

### <a name="create-an-iot-hub"></a>Création d’un IoT Hub

Si vous n’en avez pas déjà, [créez un Iot Hub](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#create-an-iot-hub).

> [!Note]
> * Le `device-to-cloud partitions` nombre n’est pas modifiable, vous devriez donc considérer l’échelle à long terme lors du nombre de partitions.
> * Le groupe de *consommateurs doit* être uniqe par consommateur. Créer un groupe de consommateurs dédié à la connexion Kusto. Trouvez votre ressource dans le portail `Built-in endpoints` Azure et allez ajouter un nouveau groupe de consommateurs.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Connexion d’ingestion de données à Azure Data Explorer

* Via Azure Portal: [Connect Azure Data Explorer table to IoT hub](https://docs.microsoft.com/azure/data-explorer/ingest-data-iot-hub#connect-azure-data-explorer-table-to-iot-hub).
* Utilisation de azure Data Explorer management .NET SDK: [Ajouter une connexion de données IoT Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-csharp#add-an-iot-hub-data-connection)
* Utilisation de La gestion Azure Data Explorer Python SDK : [Ajoutez une connexion de données IoT Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-python#add-an-iot-hub-data-connection)
* Avec le modèle ARM : [modèle Azure Resource Manager pour l’ajout d’une connexion de données Iot Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-iot-hub-resource-manager#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> Si **mes données incluent des informations de routage** sélectionnées, vous *devez* fournir les informations de [routage](#events-routing) nécessaires dans le cadre des propriétés d’événements.

> [!Note]
> Une fois la connexion définie, elle ingérer des données à partir d’événements ensemencés après son temps de création.

### <a name="generating-data"></a>Génération de données

* Voir [l’exemple de projet](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) qui simule un appareil et génère des données.