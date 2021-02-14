---
title: Ingérer à partir d’Event Hub - Azure Data Explorer
description: Cet article décrit l’ingestion à partir d’Event Hub dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: b511a5ed5ed87d6b1204152e6bbabdb24d25c85b
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554817"
---
# <a name="event-hub-data-connection"></a>Connexion de données Event Hub

[Azure Event Hubs](/azure/event-hubs/event-hubs-about) est une plateforme de streaming de Big Data et un service d’ingestion d’événements. Azure Data Explorer offre une ingestion continue à partir des hubs d’événements gérés par le client.

Le pipeline d’ingestion Event Hub transfère les événements à Azure Data Explorer en plusieurs étapes. Commencez par créer un hub d’événements dans le portail Azure. Vous créez ensuite une table cible dans Azure Data Explorer, dans laquelle les [données d’un format particulier](#data-format) sont ingérées à l’aide des [propriétés d’ingestion](#ingestion-properties) indiquées. La connexion Event Hub doit connaître le [routage des événements](#events-routing). Les données sont incorporées avec les propriétés sélectionnées en fonction du [mappage des propriétés du système d’événements](#event-system-properties-mapping). [Créez une connexion](#event-hub-connection) à Event Hub pour [créer un hub d’événements](#create-an-event-hub) et [envoyer des événements](#send-events). Ce processus peut être géré par le biais du [portail Azure](ingest-data-event-hub.md), programmatiquement avec [C#](data-connection-event-hub-csharp.md) ou [Python](data-connection-event-hub-python.md), ou avec le [modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md).

Pour obtenir des informations générales sur l’ingestion de données dans Azure Data Explorer, consultez [Vue d’ensemble de l’ingestion des données dans Azure Data Explorer](ingest-data-overview.md).

## <a name="data-format"></a>Format de données

* Les données sont lues à partir du hub d’événements sous forme d’objets [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata).
* Examinez les [formats pris en charge](ingestion-supported-formats.md).
    > [!NOTE]
    > Event Hub ne prend pas en charge le format .raw.

* Les données peuvent être compressées en utilisant l’algorithme de compression `GZip`. Spécifiez `Compression` dans les [propriétés d’ingestion](#ingestion-properties).
   * La compression des données n’est pas prise en charge pour les formats compressés (Avro, Parquet, ORC).
   * L’encodage personnalisé et les [propriétés système](#event-system-properties-mapping) incorporées ne sont pas pris en charge sur les données compressées.
  
## <a name="ingestion-properties"></a>Propriétés d’ingestion

Les propriétés d’ingestion déterminent le processus d’ingestion, où router les données et comment les traiter. Vous pouvez spécifier les [propriétés d’ingestion](ingestion-properties.md) de l’ingestion des événements avec [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties). Vous pouvez définir les propriétés suivantes :

|Propriété |Description|
|---|---|
| Table de charge de travail | Nom (sensible à la casse) de la table cible existante. Remplace le paramètre `Table` défini dans le volet `Data Connection`. |
| Format | Format de données. Remplace le paramètre `Data format` défini dans le volet `Data Connection`. |
| IngestionMappingReference | Nom du [mappage d’ingestion](kusto/management/create-ingestion-mapping-command.md) existant à utiliser. Remplace le paramètre `Column mapping` défini dans le volet `Data Connection`.|
| Compression | Compression de données, `None` (par défaut) ou compression `GZip`.|
| Encodage | Encodage des données, la valeur par défaut est UTF8. Il peut s’agir de l’un des [encodages pris en charge par .NET](/dotnet/api/system.text.encoding#remarks). |
| Étiquettes | Liste d’[étiquettes](kusto/management/extents-overview.md#extent-tagging) à associer aux données ingérées, sous forme de chaîne de tableau JSON. L’utilisation d’étiquettes a des [répercussions sur les performances](kusto/management/extents-overview.md#performance-notes-1). |

> [!NOTE]
> Seuls les événements mis en file d’attente après que vous avez créé la connexion de données sont ingérés.

## <a name="events-routing"></a>Routage d’événements

Lors de la configuration d’une connexion Event Hub au cluster Azure Data Explorer, vous spécifiez les propriétés de la table cible (nom de table, format de données, compression et mappage). Le routage par défaut pour vos données est également appelé `static routing`.
Vous pouvez également spécifier des propriétés de la table cible pour chaque événement, à l’aide des propriétés d’événement. La connexion route dynamiquement les données comme spécifié dans [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties), en remplaçant les propriétés statiques de cet événement.

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

## <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

Les propriétés système stockent les propriétés définies par le service Event Hubs, au moment de la mise en file d’attente de l’événement. La connexion Event Hub Azure Data Explorer incorpore les propriétés sélectionnées dans l’arrivage de données dans votre table.

[!INCLUDE [event-hub-system-mapping](includes/event-hub-system-mapping.md)]

### <a name="system-properties"></a>Propriétés système

Event Hub expose les propriétés système suivantes :

|Propriété |Type de données |Description|
|---|---|---|
| x-opt-enqueued-time |DATETIME | Heure UTC à laquelle l’événement a été mis en file d’attente |
| x-opt-sequence-number |long | Numéro de séquence logique de l’événement dans le flux de partition du hub d’événements
| x-opt-offset |string | Décalage de l’événement par rapport au flux de partition du hub d’événements. L’identificateur de décalage est unique au sein d’une partition du flux du hub d’événements |
| x-opt-publisher |string | Nom de l’éditeur, si le message a été envoyé à un point de terminaison d’éditeur |
| x-opt-partition-key |string |Clé de partition de la partition correspondante qui a stocké l’événement |

Si vous avez sélectionné **Propriétés du système d’événements** dans la section **Source de données** de la table, vous devez inclure les propriétés dans le schéma et le mappage de table.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="event-hub-connection"></a>Connexion au hub d’événements

> [!Note]
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Data Explorer.

### <a name="create-an-event-hub"></a>Créer un hub d’événements

[Créez un hub d’événements](/azure/event-hubs/event-hubs-create) si vous n’en avez pas déjà un. La connexion à Event Hub peut être gérée par le biais du [portail Azure](ingest-data-event-hub.md), programmatiquement avec [C#](data-connection-event-hub-csharp.md) ou [Python](data-connection-event-hub-python.md), ou avec le [modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md).


> [!Note]
> * Le nombre de partitions n’est pas modifiable. Lorsque vous le définissez, tenez compte de la mise à l’échelle sur le long terme.
> * Le groupe de consommateurs *doit* être unique par consommateur. Créez un groupe de consommateurs dédié à la connexion Azure Data Explorer.

## <a name="send-events"></a>Envoyer des événements

Consultez l’[exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) qui génère des données et les envoie à un hub d’événements.

Pour obtenir un exemple illustrant la façon de générer des exemples de données, consultez [Ingérer des données Event Hub dans Azure Data Explorer](ingest-data-event-hub.md#generate-sample-data).

## <a name="set-up-geo-disaster-recovery-solution"></a>Configurer une solution de géo-reprise d’activité après sinistre

Event Hub propose une solution de [géo-reprise d’activité après sinistre](/azure/event-hubs/event-hubs-geo-dr). Azure Data Explorer ne prend pas en charge les espaces de noms Event Hub de type `Alias`. Pour implémenter la géo-reprise d’activité après sinistre dans votre solution, créez deux connexions de données Event Hub : une pour l’espace de noms principal et une pour l’espace de noms secondaire. Azure Data Explorer écoutera les deux connexions Event Hub.

> [!NOTE]
> Il est de la responsabilité de l’utilisateur d’implémenter un basculement entre l’espace de noms principal et l’espace de noms secondaire.

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des données Event Hub dans Azure Data Explorer](ingest-data-event-hub.md)
* [Créer une connexion de données au hub d’événements pour Azure Data Explorer à l’aide de C#](data-connection-event-hub-csharp.md)
* [Créer une connexion de données au hub d’événements pour Azure Data Explorer à l’aide de Python](data-connection-event-hub-python.md)
* [Créer une connexion de données Event Hub pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md)
