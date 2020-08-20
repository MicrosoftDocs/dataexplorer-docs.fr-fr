---
title: Ingérer à partir d’Event Hub - Azure Data Explorer
description: Cet article décrit l’ingestion à partir d’Event Hub dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: 5dadde42bcdade8d7839c149cf7ca335b8a49bc8
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202403"
---
# <a name="connect-to-event-hub"></a>Connexion à Event Hub


[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) est une plateforme de streaming de Big Data et un service d’ingestion d’événements. Azure Data Explorer offre une ingestion continue à partir des hubs d’événements gérés par le client.

Le pipeline d’ingestion Event Hub transfère les événements à Azure Data Explorer en suivant plusieurs étapes. Commencez par créer un hub d’événements dans le portail Azure. Vous créez ensuite une table cible Azure Data Explorer dans laquelle les [données sous un format particulier](#data-format) sont ingérées à l’aide des [propriétés d’ingestion](#set-ingestion-properties) indiquées. La connexion Event Hub doit connaître le [routage des événements](#set-events-routing). Les données sont incorporées avec les propriétés sélectionnées en fonction du [mappage des propriétés du système d’événements](#set-event-system-properties-mapping). Ce processus peut être géré par le biais du [portail Azure](ingest-data-event-hub.md), par programmation avec [C#](data-connection-event-hub-csharp.md) ou [Python](data-connection-event-hub-python.md), ou avec le [modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md).

## <a name="data-format"></a>Format de données

* Les données sont lues à partir du hub d’événements sous forme d’objets [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet).
* La charge utile d’événement peut être ingérée dans l’un des [formats pris en charge par Azure Data Explorer](ingestion-supported-formats.md).
* Les données peuvent être compressées à l’aide de l’algorithme de compression `GZip`. Cela doit être spécifié comme [propriété d’ingestion](#set-ingestion-properties) `Compression`.

    > [!Note]
    > * La compression des données n’est pas prise en charge pour les formats compressés (Avro, Parquet, ORC).
    > * L’encodage personnalisé et les [propriétés système](#set-event-system-properties-mapping) incorporées ne sont pas pris en charge sur les données compressées.
    
## <a name="set-ingestion-properties"></a>Définir les propriétés d’ingestion

Les propriétés d’ingestion déterminent le processus d’ingestion, où router les données et comment les traiter. Vous pouvez spécifier les [propriétés d’ingestion](ingestion-properties.md) de l’ingestion des événements avec [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Vous pouvez définir les propriétés suivantes :

|Propriété |Description|
|---|---|
| Table de charge de travail | Nom (sensible à la casse) de la table cible existante. Remplace le paramètre `Table` défini dans le panneau `Data Connection`. |
| Format | Format de données. Remplace le paramètre `Data format` défini dans le panneau `Data Connection`. |
| IngestionMappingReference | Nom du [mappage d’ingestion](kusto/management/create-ingestion-mapping-command.md) existant à utiliser. Remplace le paramètre `Column mapping` défini dans le panneau `Data Connection`.|
| Compression | Compression de données, `None` (par défaut) ou compression `GZip`.|
| Encodage | Encodage des données, la valeur par défaut est UTF8. Il peut s’agir de l’un des [encodages pris en charge par .NET](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |
| Étiquettes (préversion) | Liste d’[étiquettes](kusto/management/extents-overview.md#extent-tagging) à associer aux données ingérées, sous forme de chaîne de tableau JSON. L’utilisation d’étiquettes a des [répercussions sur les performances](kusto/management/extents-overview.md#performance-notes-1). |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="set-events-routing"></a>Définir le routage des événements

Lors de la configuration d’une connexion Event Hub au cluster Azure Data Explorer, vous spécifiez les propriétés de la table cible (nom de table, format de données, compression et mappage). Le routage par défaut pour vos données est également appelé `static routing`.
Vous pouvez également spécifier des propriétés de la table cible pour chaque événement, à l’aide des propriétés d’événement. La connexion route dynamiquement les données comme spécifié dans [EventData.Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), en remplaçant les propriétés statiques de cet événement.

Dans l’exemple suivant, définissez les détails du hub d’événements et envoyez les données de métriques météorologiques à la table `WeatherMetrics`.
Les données sont au format `json`. `mapping1` est prédéfini sur la table `WeatherMetrics`.

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
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="set-event-system-properties-mapping"></a>Définir le mappage des propriétés du système d’événements

Les propriétés système stockent les propriétés définies par le service Event Hubs, au moment de la mise en file d’attente de l’événement. La connexion Event Hub Azure Data Explorer incorpore les propriétés sélectionnées dans l’arrivage de données dans votre table.

> [!Note]
> * Les propriétés système sont prises en charge pour les événements à enregistrement unique.
> * Les propriétés système ne sont pas prises en charge sur les données compressées.
> * Pour un mappage `csv`, des propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessous. Pour un mappage `json`, des propriétés sont ajoutées en fonction des noms de propriété dans le tableau suivant.

### <a name="event-hub-exposes-the-following-system-properties"></a>Event Hub expose les propriétés système suivantes

|Propriété |Type de données |Description|
|---|---|---|
| x-opt-enqueued-time |DATETIME | Heure UTC à laquelle l’événement a été mis en file d’attente |
| x-opt-sequence-number |long | Numéro de séquence logique de l’événement dans le flux de partition du hub d’événements
| x-opt-offset |string | Décalage de l’événement par rapport au flux de partition du hub d’événements. L’identificateur de décalage est unique au sein d’une partition du flux du hub d’événements |
| x-opt-publisher |string | Nom de l’éditeur, si le message a été envoyé à un point de terminaison d’éditeur |
| x-opt-partition-key |string |Clé de partition de la partition correspondante qui a stocké l’événement |

Si vous avez sélectionné **Propriétés du système d’événements** dans la section **Source de données** de la table, vous devez inclure les propriétés dans le schéma et le mappage de table.

### <a name="examples"></a>Exemples

#### <a name="table-schema-example"></a>Exemple de schéma de table

Créez ou modifiez le schéma de table à l’aide de la commande de schéma de table, si vos données incluent :
* les colonnes `Timespan`, `Metric` et `Value`  
* les propriétés `x-opt-enqueued-time` et `x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

#### <a name="csv-mapping-example"></a>Exemple de mappage CSV

Exécutez les commandes suivantes pour ajouter des données au début de l’enregistrement.
Des propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessus.
Les valeurs ordinales sont importantes pour le mappage CSV dans lequel les ordinaux de colonne changent en fonction des propriétés système mappées.

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

Ajoutez des données en utilisant les noms de propriétés système tels qu’ils apparaissent dans la liste *Propriétés du système d’événements* du panneau *Connexion de données*. Exécutez :

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
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Data Explorer.

### <a name="create-an-event-hub"></a>Créer un hub d’événements

[Créez un hub d’événements](https://docs.microsoft.com/azure/event-hubs/event-hubs-create) si vous n’en avez pas déjà un. Vous trouverez un modèle dans le guide pratique [Créer un hub d’événements](ingest-data-event-hub.md#create-an-event-hub).

> [!Note]
> * Le nombre de partitions n’est pas modifiable. Lorsque vous le définissez, tenez compte de la mise à l’échelle sur le long terme.
> * Le groupe de consommateurs *doit* être unique par consommateur. Créez un groupe de consommateurs dédié à la connexion Azure Data Explorer.

#### <a name="generate-data"></a>Générer les données

* Consultez l’[exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) qui génère des données et les envoie à un hub d’événements.

Un événement peut contenir un ou plusieurs enregistrements, jusqu’à sa limite de taille. Dans l’exemple suivant, nous envoyons deux événements, chacun avec cinq enregistrements ajoutés :

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

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des données Event Hub dans Azure Data Explorer](ingest-data-event-hub.md)
* [Créer une connexion de données au hub d’événements pour Azure Data Explorer à l’aide de C#](data-connection-event-hub-csharp.md)
* [Créer une connexion de données au hub d’événements pour Azure Data Explorer à l’aide de Python](data-connection-event-hub-python.md)
* [Créer une connexion de données Event Hub pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md)
