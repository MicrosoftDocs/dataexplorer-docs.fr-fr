---
title: Ingest from Event Hub - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Ingest de Event Hub dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ddb218e707152f529e5b547ffe06c4d3c7614811
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521503"
---
# <a name="ingest-from-event-hub"></a>Ingest de Event Hub

[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) est une plateforme de streaming big data et un service d’ingestion d’événements. Azure Data Explorer offre une ingestion continue de Hubs Event gérés par le client. 

## <a name="data-format"></a>Format de données

* Les données sont lues à partir du Event Hub sous forme d’objets [EventData.](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet)
* La charge utile d’événement peut contenir un ou plusieurs enregistrements à ingérer, dans l’un des [formats pris en charge par Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).
* Les données peuvent `GZip` être compressées à l’aide d’algorithme de compression. Doit être `Compression` spécifié comme [propriété d’ingestion](#ingestion-properties).

> [!Note]
> * La compression des données n’est pas prise en charge pour les formats compressés (Avro, Parquet, ORC).
> * Les [propriétés personnalisées](#event-system-properties-mapping) de système d’encodage et d’intégration ne sont pas prises en charge sur les données compressées.

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Les propriétés d’ingestion instruisent le processus d’ingestion. Où acheminer les données et comment les traiter. Vous pouvez spécifier les [propriétés Ingestion](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) de l’ingestion des événements à l’aide de [l’EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Vous pouvez définir les propriétés suivantes :

|Propriété |Description|
|---|---|
| Table de charge de travail | Nom (sensible au cas) de la table cible existante. Remplace l’ensemble `Table` sur `Data Connection` la lame. |
| Format | Format de données. Remplace l’ensemble `Data format` sur `Data Connection` la lame. |
| IngestionMappingReference | Nom de la [cartographie d’ingestion](../create-ingestion-mapping-command.md) existante à utiliser. Remplace l’ensemble `Column mapping` sur `Data Connection` la lame.|
| Compression | Compression de `None` données, `GZip` (par défaut) ou compression.|
| Encodage |  L’encodage des données, la valeur par défaut est UTF8. Peut être l’un des [encodages supportés .NET](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>Itinéraire d’événements

Lors de la configuration d’une connexion Event Hub au cluster Azure Data Explorer, vous spécifiez les propriétés de la table cible (nom de table, format de données, compression et cartographie). Il s’agit du routage par défaut `static routing`pour vos données, également appelé .
Vous pouvez également spécifier les propriétés de table cible pour chaque événement, en utilisant les propriétés de l’événement. La connexion permettra d’acheminer dynamiquement les données comme spécifié dans le [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), l’emporter sur les propriétés statiques pour cet événement.

Dans l’échantillon suivant, définissez les détails `WeatherMetrics`du moyeu d’événement et envoyez des données métriques météorologiques à la table.
Les données `json` sont en format. `mapping1`est prédéfinis `WeatherMetrics`sur la table :

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

Les propriétés du système sont une collection utilisée pour stocker les propriétés qui sont définies par le service Event Hubs, au moment où l’événement est enqueued. La connexion Azure Data Explorer Event Hub intégrera les propriétés sélectionnées dans l’atterrissage de données dans votre table.

> [!Note]
> * Les propriétés du système sont prises en charge pour les événements à un seul enregistrement.
> * Les propriétés du système ne sont pas prises en charge sur les données compressées.
> * Pour `csv` la cartographie, les propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessous. Pour `json` la cartographie, les propriétés sont ajoutées en fonction des noms de propriété dans le tableau suivant.

### <a name="event-hub-expose-the-following-system-properties"></a>Event Hub expose les propriétés système suivantes

|Propriété |Type de données |Description|
|---|---|---|
| x-opt-enqueued-time |DATETIME | UTC heure où l’événement a été enqueued. |
| x-opt-sequence-number |long | Le numéro de séquence logique de l’événement dans le flux de partition du Event Hub.
| x-opt-offset |string | Le décalage de l’événement par rapport au flux de partition Event Hub. L’identifiant offset est unique dans une partition du flux Event Hub. |
| x-opt-éditeur |string | Le nom de l’éditeur si le message a été envoyé à un point de terminaison de l’éditeur. |
| x-opt-partition-key |string |La clé de partition de la partition correspondante qui a stocké l’événement. |

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

## <a name="create-event-hub-connection"></a>Créer une connexion de hub d’événements

> [!Note]
> Pour de meilleures performances, créez toutes les ressources dans la même région que le cluster Azure Data Explorer.

### <a name="create-an-event-hub"></a>Création d’un concentrateur d’événements

Si vous n’en avez pas déjà, [créez un hub d’événement.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) Un modèle peut être trouvé dans le guide de création [d’un hub d’événements.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub)

> [!Note]
> * Le nombre de partitions n’est pas modifiable. Lorsque vous le définissez, tenez compte de la mise à l’échelle sur le long terme.
> * Le groupe de *consommateurs doit* être uniqe par consommateur. Créer un groupe de consommateurs dédié à la connexion Azure Data Explorer.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Connexion d’ingestion de données à Azure Data Explorer

* Via Azure Portal: [Connectez-vous au hub de l’événement](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub).
* Utilisation de la gestion Azure Data Explorer .NET SDK: [Ajouter une connexion de données Event Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)
* Utilisation de Python SDK, gestion d’Azure Data Explorer : [Ajoutez une connexion de données Event Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)
* Avec le modèle ARM : [modèle Azure Resource Manager pour l’ajout d’une connexion de données Event Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> Si **mes données incluent des informations de routage** sélectionnées, vous *devez* fournir les informations de [routage](#events-routing) nécessaires dans le cadre des propriétés d’événements.

> [!Note]
> Une fois la connexion définie, elle ingérer des données à partir d’événements ensemencés après son temps de création.

#### <a name="generating-data"></a>Génération de données

* Consultez [l’application d’échantillon](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) qui génère des données et les envoie à un hub d’événements.

Un événement peut contenir un ou plusieurs enregistrements, jusqu’à sa limite de taille. Dans l’échantillon suivant, nous envoyons deux événements, chacun a 5 enregistrements annexés:

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) }; 
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```