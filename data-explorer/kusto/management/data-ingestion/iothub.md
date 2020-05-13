---
title: Réception à partir de IoT Hub-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la réception à partir de IoT Hub dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: c3ed0fa8608f2be5739c1143a648230f792e5d40
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373388"
---
# <a name="ingest-from-iot-hub"></a>Ingérer à partir d’IoT Hub

[Azure IOT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub) est un service géré, hébergé dans le Cloud, qui sert de concentrateur de messages central pour la communication bidirectionnelle entre votre application IOT et les appareils qu’elle gère. Azure Explorateur de données offre une ingestion continue des hubs IoT gérés par le client, à l’aide de son [point de terminaison intégré compatible avec Event Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c#routing-endpoints).

## <a name="data-format"></a>Format de données

* Les données sont lues à partir du point de terminaison Event Hub sous forme d’objets [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) .
* La charge utile d’un événement peut être dans l’un des [formats pris en charge par Azure Explorateur de données](../../../ingestion-supported-formats.md).

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Les propriétés d’ingestion indiquent au processus d’ingestion. Où acheminer les données et comment les traiter. Vous pouvez spécifier les [Propriétés](../../../ingestion-properties.md) d’ingestion de l’ingestion des événements à l’aide de [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Vous pouvez définir les propriétés suivantes :

|Propriété |Description|
|---|---|
| Table de charge de travail | Nom (sensible à la casse) de la table cible existante. Remplace le paramètre `Table` défini dans le panneau `Data Connection`. |
| Format | Format de données. Remplace le paramètre `Data format` défini dans le panneau `Data Connection`. |
| IngestionMappingReference | Nom du [mappage](../create-ingestion-mapping-command.md) d’ingestion existant à utiliser. Remplace le paramètre `Column mapping` défini dans le panneau `Data Connection`.|
| Encodage |  L’encodage des données, la valeur par défaut est UTF8. Il peut s’agir de l’un des [encodages .net pris en charge](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |

## <a name="events-routing"></a>Routage des événements

Lors de la configuration d’une connexion IoT Hub à Azure Explorateur de données cluster, vous spécifiez les propriétés de la table cible (nom de table, format de données et mappage). Il s’agit du routage par défaut pour vos données, également appelé `static routing` .
Vous pouvez également spécifier des propriétés de la table cible pour chaque événement, à l’aide des propriétés de l’événement. La connexion achemine dynamiquement les données telles qu’elles sont spécifiées dans [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), en remplaçant les propriétés statiques de cet événement.

## <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

Les propriétés système sont un regroupement utilisé pour stocker les propriétés définies par le service IoT hubs, à l’heure de réception de l’événement. La connexion IoT Hub Azure Explorateur de données incorpore les propriétés sélectionnées dans le lancement des données dans votre table.

> [!Note]
> * Pour le `csv` mappage, les propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessous. Pour `json` le mappage, les propriétés sont ajoutées en fonction des noms de propriété dans le tableau suivant.

### <a name="iot-hub-exposes-the-following-system-properties"></a>IoT Hub expose les propriétés système suivantes :

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

Si vous avez sélectionné **Propriétés du système d’événements** dans la section source de **données** de la table, vous devez inclure les propriétés dans le schéma et le mappage de la table.

**Exemple de schéma de table**

Si vos données incluent trois colonnes ( `Timespan` , `Metric` et `Value` ) et que les propriétés que vous incluez sont `x-opt-enqueued-time` et `x-opt-offset` , créez ou modifiez le schéma de la table à l’aide de cette commande :

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**Exemple de mappage de fichier CSV**

Exécutez les commandes suivantes pour ajouter des données au début de l’enregistrement. Notez les valeurs ordinales : les propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessus. Cela est important pour le mappage de volumes partagés de cluster dans lequel les ordinaux de colonne changent en fonction des propriétés système mappées.

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
 
**Exemple de mappage JSON**

Les données sont ajoutées à l’aide des noms de propriétés système tels qu’ils apparaissent dans la liste des **Propriétés système des événements** du panneau **connexion de données** . Exécutez les commandes suivantes :

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

## <a name="create-iot-hub-connection"></a>Créer IoT Hub connexion

> [!Note]
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Explorateur de données.

### <a name="create-an-iot-hub"></a>Création d’un IoT Hub

Si vous n’en avez pas déjà un, [créez un IOT Hub](../../../ingest-data-iot-hub.md#create-an-iot-hub).

> [!Note]
> * Le `device-to-cloud partitions` nombre n’étant pas modifiable, vous devez envisager une mise à l’échelle à long terme lors de la définition du nombre de partitions.
> * Le groupe de consommateurs *doit* être uniqe par consommateur. Créez un groupe de consommateurs dédié à la connexion Kusto. Recherchez votre ressource dans le portail Azure et accédez à `Built-in endpoints` pour ajouter un nouveau groupe de consommateurs.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Connexion d’ingestion de données à Azure Explorateur de données

* Via le portail Azure : [Connectez le tableau de Explorateur de données Azure à IOT Hub](../../../ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub).
* Utilisation du kit de développement logiciel (SDK) Azure Explorateur de données Management .NET : [Ajouter une connexion de données IOT Hub](../../../data-connection-iot-hub-csharp.md#add-an-iot-hub-data-connection)
* Utilisation du kit de développement logiciel (SDK) python Azure Explorateur de données Management : [Ajouter une connexion de données IOT Hub](../../../data-connection-iot-hub-python.md#add-an-iot-hub-data-connection)
* Avec le modèle ARM : [Azure Resource Manager modèle pour l’ajout d’une connexion de données IOT Hub](../../../data-connection-iot-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-iot-hub-data-connection)

> [!Note]
> Si **mes données incluent** des informations de routage sélectionnées, vous *devez* fournir les informations de [routage](#events-routing) nécessaires dans le cadre des propriétés des événements.

> [!Note]
> Une fois la connexion définie, les données sont ingérées à partir des événements mis en file d’attente après l’heure de leur création.

### <a name="generating-data"></a>Génération de données

* Consultez l' [exemple de projet](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Quickstarts/simulated-device) qui simule un appareil et génère des données.