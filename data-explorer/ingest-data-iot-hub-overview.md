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
ms.openlocfilehash: 8a009c82f787dac0bd4a86209b07ffc14d9ec8cf
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874866"
---
# <a name="connect-to-iot-hub"></a>Connexion à IoT Hub

[Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) est un service managé, hébergé dans le cloud, qui fait office de hub de messages central pour la communication bidirectionnelle entre votre application IoT et les appareils qu’il gère. Azure Data Explorer offre une ingestion continue à partir des hubs IoT gérés par le client, à l’aide de son [point de terminaison intégré compatible avec Event Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints).

Le pipeline d’ingestion IoT passe par plusieurs étapes. Tout d’abord, vous créez un hub IoT et inscrivez un appareil auprès de ce hub. Vous créez ensuite une table cible Azure Data Explorer dans laquelle les [données sous un format particulier](#data-format) sont ingérées à l’aide des [propriétés d’ingestion](#set-ingestion-properties) indiquées. La connexion Iot Hub doit connaître le [routage des événements](#set-events-routing) pour se connecter à la table Azure Data Explorer. Les données sont incorporées avec les propriétés sélectionnées en fonction du [mappage des propriétés du système d’événements](#set-event-system-properties-mapping). Ce processus peut être géré par le biais du [portail Azure](ingest-data-iot-hub.md), par programmation avec [C#](data-connection-iot-hub-csharp.md) ou [Python](data-connection-iot-hub-python.md), ou avec le [modèle Azure Resource Manager](data-connection-iot-hub-resource-manager.md).


## <a name="create-iot-hub-connection"></a>Créer une connexion Iot Hub

> [!Note]
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Data Explorer.

[Créez un hub IoT](ingest-data-iot-hub.md#create-an-iot-hub) si vous n’en avez pas déjà un.

> [!Note]
> * Le nombre de `device-to-cloud partitions` n’étant pas modifiable, définissez-le dans une perspective à long terme.
> * Le groupe de consommateurs doit être unique par consommateur. Créez un groupe de consommateurs dédié à la connexion Azure Data Explorer. Recherchez votre ressource dans le portail Azure et accédez à `Built-in endpoints` pour ajouter un nouveau groupe de consommateurs.

## <a name="data-format"></a>Format de données

* Les données sont lues à partir du point de terminaison Event Hub sous forme d’objets [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet).
* La charge utile d’événement peut être ingérée dans l’un des [formats pris en charge par Azure Data Explorer](ingestion-supported-formats.md).
* Examinez les [compressions prises en charge](ingestion-supported-formats.md#supported-data-compression-formats).
  La taille des données non compressées d’origine doit faire partie des métadonnées d’objets blob, sinon Azure Data Explorer l’estime. La limite de taille décompressée d’ingestion par fichier est de 4 Go.  

## <a name="set-ingestion-properties"></a>Définir les propriétés d’ingestion

Les propriétés d’ingestion déterminent le processus d’ingestion, où router les données et comment les traiter. Vous pouvez spécifier les [propriétés d’ingestion](ingestion-properties.md) de l’ingestion des événements avec [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Vous pouvez définir les propriétés suivantes :

|Propriété |Description|
|---|---|
| Table de charge de travail | Nom (sensible à la casse) de la table cible existante. Remplace le paramètre `Table` défini dans le panneau `Data Connection`. |
| Format | Format de données. Remplace le paramètre `Data format` défini dans le panneau `Data Connection`. |
| IngestionMappingReference | Nom du [mappage d’ingestion](kusto/management/create-ingestion-mapping-command.md) existant à utiliser. Remplace le paramètre `Column mapping` défini dans le panneau `Data Connection`.|
| Encodage |  Encodage des données, la valeur par défaut est UTF8. Il peut s’agir de l’un des [encodages pris en charge par .NET](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |

## <a name="set-events-routing"></a>Définir le routage des événements

Lors de la configuration d’une connexion Iot Hub au cluster Azure Data Explorer, vous spécifiez les propriétés de la table cible (nom de table, format de données et mappage). Il s’agit du routage par défaut pour vos données, également appelé routage statique.
Vous pouvez également spécifier des propriétés de la table cible pour chaque événement, à l’aide des propriétés d’événement. La connexion route dynamiquement les données comme spécifié dans [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), en remplaçant les propriétés statiques de cet événement.

> [!Note]
> Si l’option **Mes données contiennent des informations de routage** est sélectionnée, vous devez fournir les informations de routage nécessaires dans le cadre des propriétés des événements.

## <a name="set-event-system-properties-mapping"></a>Définir le mappage des propriétés du système d’événements

Les propriétés système sont un regroupement utilisé pour stocker les propriétés définies par le service IoT Hub, au moment de la réception de l’événement. La connexion Iot Hub Azure Data Explorer incorpore les propriétés sélectionnées dans l’arrivage de données dans votre table.

> [!Note]
> Pour un mappage `csv`, des propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessous. Pour un mappage `json`, des propriétés sont ajoutées en fonction des noms de propriété dans le tableau suivant.

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Hub expose les propriétés système suivantes :

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

### <a name="examples"></a>Exemples 

#### <a name="table-schema-example"></a>Exemple de schéma de table

Si vos données comprennent trois colonnes (`Timespan`, `Metric`et `Value`) et que les propriétés que vous incluez sont `x-opt-enqueued-time` et `x-opt-offset`, créez ou modifiez le schéma de table à l’aide de la commande suivante :

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

#### <a name="csv-mapping-example"></a>Exemple de mappage CSV

Exécutez les commandes suivantes pour ajouter des données au début de l’enregistrement. Notez les valeurs ordinales : des propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessous. Cela est important pour le mappage CSV dans lequel les ordinaux de colonne changent en fonction des propriétés système mappées.

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
 
#### <a name="json-mapping-example"></a>Exemple de mappage JSON

Des données sont ajoutées en utilisant les noms de propriétés système tels qu’ils apparaissent dans la liste **Propriétés système d’événement** du panneau **Connexion de données**. Exécutez les commandes suivantes :

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

### <a name="generate-data"></a>Générer les données

* Consultez l’[exemple de projet](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) qui simule un appareil et génère des données.

## <a name="next-steps"></a>Étapes suivantes

Il existe différentes méthodes pour ingérer des données dans IoT Hub. Consultez les liens suivants pour obtenir des procédures pas à pas de chaque méthode.

* [Ingérer les données d’un hub IoT dans Azure Data Explorer](ingest-data-iot-hub.md)
* [Créer une connexion de données au hub IoT pour Azure Data Explorer à l’aide de C# (préversion)](data-connection-iot-hub-csharp.md)
* [Créer une connexion de données au hub IoT pour Azure Data Explorer à l’aide de Python (préversion)](data-connection-iot-hub-python.md)
* [Créer une connexion de données IoT Hub pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager](data-connection-iot-hub-resource-manager.md)
