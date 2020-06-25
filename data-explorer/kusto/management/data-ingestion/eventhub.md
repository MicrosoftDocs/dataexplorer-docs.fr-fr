---
title: Réception à partir d’Event Hub-Azure Explorateur de données
description: Cet article décrit la réception à partir d’Event Hub dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: dbb74d8b718b5a03fa0e9e7f4679f48c837d0842
ms.sourcegitcommit: c3bbb9a6bfd7c5506f05afb4968fdc2043a9fbbf
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/24/2020
ms.locfileid: "85332553"
---
# <a name="ingest-from-event-hub"></a>Ingérer à partir d’Event Hub

[Azure Event hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) est une plateforme de diffusion en continu Big Data et un service d’ingestion des événements. Azure Explorateur de données offre une ingestion continue des Event Hubs gérés par le client.

## <a name="data-format"></a>Format de données

* Les données sont lues à partir du hub d’événements sous forme d’objets [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) .
* La charge utile d’un événement peut contenir un ou plusieurs enregistrements qui seront ingérés dans l’un des [formats pris en charge par Azure Explorateur de données](../../../ingestion-supported-formats.md).
* Les données peuvent être compressées à l’aide de l' `GZip` algorithme de compression. Doit être spécifié en tant que propriété d’ingestion `Compression` [ingestion property](#ingestion-properties).

> [!Note]
> * La compression des données n’est pas prise en charge pour les formats compressés (Avro, parquet, ORC).
> * L’encodage personnalisé et les [Propriétés système](#event-system-properties-mapping) incorporées ne sont pas pris en charge sur les données compressées.

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Les propriétés d’ingestion indiquent au processus d’ingestion, où acheminer les données et comment les traiter. Vous pouvez spécifier les [Propriétés](../../../ingestion-properties.md) d’ingestion de l’ingestion des événements à l’aide de [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties). Vous pouvez définir les propriétés suivantes :

|Propriété |Description|
|---|---|
| Table de charge de travail | Nom (sensible à la casse) de la table cible existante. Remplace le paramètre `Table` défini dans le panneau `Data Connection`. |
| Format | Format de données. Remplace le paramètre `Data format` défini dans le panneau `Data Connection`. |
| IngestionMappingReference | Nom du [mappage](../create-ingestion-mapping-command.md) d’ingestion existant à utiliser. Remplace le paramètre `Column mapping` défini dans le panneau `Data Connection`.|
| Compression | Compression de données, `None` (valeur par défaut) ou `GZip` compression.|
| Encodage | L’encodage des données, la valeur par défaut est UTF8. Il peut s’agir de l’un des [encodages .net pris en charge](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks). |
| Balises (préversion) | Liste de [balises](../extents-overview.md#extent-tagging) à associer aux données ingérées, au format de chaîne de tableau JSON. L’utilisation de balises présente des [implications en termes de performances](../extents-overview.md#performance-notes-1) . |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="events-routing"></a>Routage des événements

Quand vous configurez une connexion de hub d’événements à Azure Explorateur de données cluster, vous spécifiez les propriétés de la table cible (nom de table, format de données, compression et mappage). Le routage par défaut de vos données est également appelé `static routing` .
Vous pouvez également spécifier des propriétés de la table cible pour chaque événement, à l’aide des propriétés de l’événement. La connexion achemine dynamiquement les données telles qu’elles sont spécifiées dans [EventData. Properties](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties), en remplaçant les propriétés statiques de cet événement.

Dans l’exemple suivant, définissez Event Hub détails et envoyez les données de métriques météorologiques à la table `WeatherMetrics` .
Les données sont au `json` format. `mapping1`est prédéfini sur la table `WeatherMetrics` .

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

## <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

Les propriétés système stockent les propriétés définies par le service Event Hubs, au moment où l’événement est mis en file d’attente. La connexion Azure Explorateur de données Event Hub incorpore les propriétés sélectionnées dans le lancement des données dans votre table.

> [!Note]
> * Les propriétés système sont prises en charge pour les événements à enregistrement unique.
> * Les propriétés système ne sont pas prises en charge sur les données compressées.
> * Pour le `csv` mappage, les propriétés sont ajoutées au début de l’enregistrement dans l’ordre indiqué dans le tableau ci-dessous. Pour `json` le mappage, les propriétés sont ajoutées en fonction des noms de propriété dans le tableau suivant.

### <a name="event-hub-exposes-the-following-system-properties"></a>Event Hub expose les propriétés système suivantes

|Propriété |Type de données |Description|
|---|---|---|
| x-opt-enqueued-time |DATETIME | Heure UTC à laquelle l’événement a été mis en file d’attente |
| x-opt-sequence-number |long | Numéro de séquence logique de l’événement dans le flux de partition du concentrateur d’événements
| x-opt-offset |string | Décalage de l’événement à partir du flux de partition du concentrateur d’événements. L’identificateur de décalage est unique au sein d’une partition du flux de concentrateur d’événements |
| x-opt-serveur de publication |string | Nom de l’éditeur, si le message a été envoyé à un point de terminaison de serveur de publication |
| x-opt-partition-key |string |Clé de partition de la partition correspondante qui a stocké l’événement. |

Si vous avez sélectionné **Propriétés du système d’événements** dans la section source de **données** de la table, vous devez inclure les propriétés dans le schéma et le mappage de la table.

**Exemple de schéma de table**

Créez ou modifiez le schéma de la table à l’aide de la commande schéma de table, si vos données incluent :
* les colonnes `Timespan` , `Metric` et`Value`  
* les propriétés `x-opt-enqueued-time` et`x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

**Exemple de mappage de fichier CSV**

Exécutez les commandes suivantes pour ajouter des données au début de l’enregistrement.
Les propriétés sont ajoutées au début de l’enregistrement, dans l’ordre indiqué dans le tableau ci-dessus.
Les valeurs ordinales sont importantes pour le mappage de volumes partagés de cluster dans lequel les ordinaux de colonne changent, en fonction des propriétés système mappées.

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

Ajoutez des données à l’aide des noms de propriétés système tels qu’ils apparaissent dans la liste des *Propriétés système des événements* du panneau *connexion de données* . Exécutez :

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

## <a name="create-event-hub-connection"></a>Créer Event Hub connexion

> [!Note]
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Explorateur de données.

### <a name="create-an-event-hub"></a>Créer un hub d’événements

Si vous n’en avez pas déjà un, [créez un Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create). Un modèle est disponible dans le Guide de création d' [un Event Hub](../../../ingest-data-event-hub.md#create-an-event-hub) .

> [!Note]
> * Le nombre de partitions n’est pas modifiable. Lorsque vous le définissez, tenez compte de la mise à l’échelle sur le long terme.
> * Le groupe de consommateurs *doit* être unique par consommateur. Créez un groupe de consommateurs dédié à la connexion Azure Explorateur de données.

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Connexion d’ingestion de données à Azure Explorateur de données

* Via Portail Azure : [Connectez-vous au Event Hub](../../../ingest-data-event-hub.md#connect-to-the-event-hub).
* Utiliser le kit de développement logiciel (SDK) Azure Explorateur de données Management .NET : [Ajouter une connexion de données Event Hub](../../../data-connection-event-hub-csharp.md#add-an-event-hub-data-connection)
* Utiliser le kit de développement logiciel (SDK) python Azure Explorateur de données Management : [Ajouter une connexion de données Event Hub](../../../data-connection-event-hub-python.md#add-an-event-hub-data-connection)
* Avec le modèle ARM : utiliser [Azure Resource Manager modèle pour l’ajout d’une connexion de données Event Hub](../../../data-connection-event-hub-resource-manager.md#azure-resource-manager-template-for-adding-an-event-hub-data-connection)

> [!Note]
> Si **mes données incluent** des informations de routage sélectionnées, vous *devez* fournir les informations de [routage](#events-routing) nécessaires dans le cadre des propriétés des événements.

> [!Note]
> Une fois la connexion définie, elle ingère les données à partir des événements mis en file d’attente après son heure de création.

#### <a name="generating-data"></a>Génération des données

* Consultez l' [exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) qui génère des données et les envoie à un Event Hub.

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
