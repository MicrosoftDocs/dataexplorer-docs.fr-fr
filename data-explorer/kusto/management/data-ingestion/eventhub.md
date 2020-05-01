---
title: Réception à partir d’Event Hub-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la réception à partir d’Event Hub dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: fac9fd9f218948928e4f91d0d1aa056affcebd11
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617661"
---
# <a name="ingest-from-event-hub"></a>Ingérer à partir d’Event Hub

[Azure Event hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) est une plateforme de diffusion en continu Big Data et un service d’ingestion des événements. Azure Explorateur de données offre une ingestion continue des Event Hubs gérés par le client. 

## <a name="data-format"></a>Format de données

* Les données sont lues à partir du hub d’événements sous forme d’objets [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) .
* La charge utile d’un événement peut contenir un ou plusieurs enregistrements à ingérer, dans l’un des [formats pris en charge par Azure Explorateur de données](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).
* Les données peuvent être compressées à l’aide de l' `GZip` algorithme de compression. Doit être spécifié en `Compression` tant que [propriété](#ingestion-properties)d’ingestion.

> [!Note]
> * La compression des données n’est pas prise en charge pour les formats compressés (Avro, parquet, ORC).
> * L’encodage personnalisé et les [Propriétés système](#event-system-properties-mapping) incorporées ne sont pas pris en charge sur les données compressées.

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Les propriétés d’ingestion indiquent au processus d’ingestion. Où acheminer les données et comment les traiter. Vous pouvez spécifier les [Propriétés](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) d’ingestion de l’ingestion des événements à l’aide de [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Vous pouvez définir les propriétés suivantes :

|Propriété |Description|
|---|---|
| Table de charge de travail | Nom (sensible à la casse) de la table cible existante. Remplace le `Table` défini sur le `Data Connection` panneau. |
| Format | Format de données. Remplace le `Data format` défini sur le `Data Connection` panneau. |
| IngestionMappingReference | Nom du [mappage](../create-ingestion-mapping-command.md) d’ingestion existant à utiliser. Remplace le `Column mapping` défini sur le `Data Connection` panneau.|
| Compression | Compression de données `None` , (valeur par `GZip` défaut) ou compression.|
| Encodage |  L’encodage des données, la valeur par défaut est UTF8. Il peut s’agir de l’un des [encodages .net pris en charge](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>Routage des événements

Lors de la configuration d’une connexion de hub d’événements à Azure Explorateur de données cluster, vous spécifiez les propriétés de la table cible (nom de table, format de données, compression et mappage). Il s’agit du routage par défaut pour vos données, également appelé `static routing`.
Vous pouvez également spécifier des propriétés de la table cible pour chaque événement, à l’aide des propriétés de l’événement. La connexion achemine dynamiquement les données telles qu’elles sont spécifiées dans [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), en remplaçant les propriétés statiques de cet événement.

Dans l’exemple suivant, définissez Event Hub détails et envoyez les données de métriques `WeatherMetrics`météorologiques à la table.
Les données sont `json` au format. `mapping1`est prédéfini sur la table `WeatherMetrics`:

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

Les propriétés système sont un regroupement utilisé pour stocker les propriétés définies par le service Event Hubs, au moment où l’événement est mis en file d’attente. La connexion Azure Explorateur de données Event Hub incorpore les propriétés sélectionnées dans le lancement des données dans votre table.

> [!Note]
> * Les propriétés système sont prises en charge pour les événements à enregistrement unique.
> * Les propriétés système ne sont pas prises en charge sur les données compressées.
> * Pour `csv` le mappage, les propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessous. Pour `json` le mappage, les propriétés sont ajoutées en fonction des noms de propriété dans le tableau suivant.

### <a name="event-hub-expose-the-following-system-properties"></a>Event Hub expose les propriétés système suivantes

|Propriété |Type de données |Description|
|---|---|---|
| x-opt-enqueued-time |DATETIME | Heure UTC à laquelle l’événement a été mis en file d’attente. |
| x-opt-sequence-number |long | Numéro de séquence logique de l’événement dans le flux de partition du concentrateur d’événements.
| x-opt-offset |string | Décalage de l’événement par rapport au flux de partition du concentrateur d’événements. L’identificateur de décalage est unique au sein d’une partition du flux de concentrateur d’événements. |
| x-opt-serveur de publication |string | Nom de l’éditeur si le message a été envoyé à un point de terminaison de serveur de publication. |
| x-opt-partition-key |string |Clé de partition de la partition correspondante qui a stocké l’événement. |

Si vous avez sélectionné **Propriétés du système d’événements** dans la section source de **données** de la table, vous devez inclure les propriétés dans le schéma et le mappage de la table.

**Exemple de schéma de table**

Si vos données incluent trois colonnes (`Timespan`, `Metric`et `Value`) et que les propriétés que vous incluez sont `x-opt-enqueued-time` et `x-opt-offset`, créez ou modifiez le schéma de la table à l’aide de cette commande :

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

## <a name="create-event-hub-connection"></a>Créer une connexion de hub d’événements

> [!Note]
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Explorateur de données.

### <a name="create-an-event-hub"></a>Création d’un concentrateur d’événements

Si vous n’en avez pas déjà un, [créez un Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create). Un modèle est disponible dans le Guide de création d' [un Event Hub](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#create-an-event-hub) .

> [!Note]
> * Le nombre de partitions n’est pas modifiable. Lorsque vous le définissez, tenez compte de la mise à l’échelle sur le long terme.
> * Le groupe de consommateurs *doit* être uniqe par consommateur. Créez un groupe de consommateurs dédié à la connexion Azure Explorateur de données.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Connexion d’ingestion de données à Azure Explorateur de données

* Via le portail Azure : [Connectez-vous au Event Hub](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub).
* Utilisation du kit de développement logiciel (SDK) Azure Explorateur de données Management .NET : [Ajouter une connexion de données Event Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-csharp#add-an-event-hub-data-connection)
* Utilisation du kit de développement logiciel (SDK) python Azure Explorateur de données Management : [Ajouter une connexion de données Event Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-python#add-an-event-hub-data-connection)
* Avec le modèle ARM : [Azure Resource Manager modèle pour l’ajout d’une connexion de données Event Hub](https://docs.microsoft.com/azure/data-explorer/data-connection-event-hub-resource-manager#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> Si **mes données incluent** des informations de routage sélectionnées, vous *devez* fournir les informations de [routage](#events-routing) nécessaires dans le cadre des propriétés des événements.

> [!Note]
> Une fois la connexion définie, les données sont ingérées à partir des événements mis en file d’attente après l’heure de leur création.

#### <a name="generating-data"></a>Génération de données

* Consultez l' [exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) qui génère des données et les envoie à un Event Hub.

Un événement peut contenir un ou plusieurs enregistrements, jusqu’à sa limite de taille. Dans l’exemple suivant, nous envoyons deux événements, chacun avec 5 enregistrements ajoutés :

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
