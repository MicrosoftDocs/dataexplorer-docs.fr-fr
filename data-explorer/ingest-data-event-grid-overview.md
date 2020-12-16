---
title: Ingérer à partir du stockage avec un abonnement Event Grid - Azure Data Explorer
description: Cet article décrit l’ingestion à partir du stockage avec un abonnement Event Grid dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: fde0e79fbe8a8080fa6e21dde12434de5c92353f
ms.sourcegitcommit: d9e203a54b048030eeb6d05b01a65902ebe4e0b8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/14/2020
ms.locfileid: "97371456"
---
# <a name="event-grid-data-connection"></a>Connexion de données Event Grid

L’ingestion Event Grid est un pipeline qui écoute le stockage Azure et met à jour Azure Data Explorer pour extraire des informations quand des événements associés à des abonnements se produisent. Azure Data Explorer offre une ingestion continue depuis Stockage Azure (Stockage Blob et ADLSv2) avec un abonnement [Azure Event Grid](/azure/event-grid/overview) pour des notifications d’objet blob créé ou renommé, et le streaming de ces notifications vers Azure Data Explorer via un hub d’événements.

Le pipeline d’ingestion Event Grid passe par plusieurs étapes. Vous créez une table cible dans Azure Data Explorer dans laquelle les [données d’un format particulier](#data-format) sont ingérées. Vous créez ensuite une connexion de données Event Grid dans Azure Data Explorer. La connexion de données Event Grid doit connaître les informations de [routage des événements](#events-routing), telles que la table à laquelle envoyer les données et le mappage de table. Vous spécifiez également les [propriétés d’ingestion](#ingestion-properties), qui décrivent les données à ingérer, la table cible et le mappage. Vous pouvez générer des exemples de données et [charger des objets blob](#upload-blobs) pour tester votre connexion. [Supprimez des objets blob](#delete-blobs-using-storage-lifecycle) après ingestion. Ce processus peut être géré par le biais du [portail Azure](ingest-data-event-grid.md), à l’aide de l’[ingestion en un clic](one-click-ingestion-new-table.md), par programmation avec du code [C#](data-connection-event-grid-csharp.md) ou [Python](data-connection-event-grid-python.md), ou avec un [modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md). 

Pour obtenir des informations générales sur l’ingestion de données dans Azure Data Explorer, consultez [Vue d’ensemble de l’ingestion des données dans Azure Data Explorer](ingest-data-overview.md).

## <a name="data-format"></a>Format de données

* Examinez les [formats pris en charge](ingestion-supported-formats.md).
* Examinez les [compressions prises en charge](ingestion-supported-formats.md#supported-data-compression-formats).
    * La taille des données non compressées d’origine doit faire partie des métadonnées d’objets blob, sinon Azure Data Explorer l’estime. La limite de taille décompressée d’ingestion par fichier est de 4 Go.

> [!NOTE]
> L’abonnement aux notifications Event Grid peut être défini sur des comptes de stockage Azure pour `BlobStorage`, `StorageV2` ou [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction).

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Vous pouvez spécifier les [propriétés d’ingestion](ingestion-properties.md) de l’ingestion d’objets blob via les métadonnées d’objet blob.
Vous pouvez définir les propriétés suivantes :

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

## <a name="events-routing"></a>Routage d’événements

Quand vous configurez une connexion de stockage Blob vers un cluster Azure Data Explorer, spécifiez les propriétés de la table cible :
* Nom de la table
* format de données
* mapping

Cette configuration est le routage par défaut pour vos données, parfois appelé `static routing`.
Vous pouvez également spécifier des propriétés de la table cible pour chaque objet blob, à l’aide des métadonnées d’objet blob. Les données sont routées dynamiquement, comme spécifié par les [propriétés d’ingestion](#ingestion-properties).

L’exemple suivant montre comment définir les propriétés d’ingestion sur les métadonnées de l’objet blob avant de le charger. Les objets blob sont routés vers différentes tables.

Pour plus d’informations, consultez [Charger des objets blob](#upload-blobs).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="upload-blobs"></a>Charger des objets blob

Vous pouvez créer un objet blob à partir d’un fichier local, définir des propriétés d’ingestion sur les métadonnées de l’objet blob, puis le charger. Par exemple, consultez [Ingérer des objets blob dans Azure Data Explorer en s’abonnant à des notifications Event Grid](ingest-data-event-grid.md#generate-sample-data).

> [!NOTE]
> * Utilisez `BlockBlob` pour générer les données. `AppendBlob` n’est pas pris en charge.
> * L’utilisation du SDK de stockage Azure Data Lake Gen2 requiert l’utilisation de `CreateFile` pour charger des fichiers et de `Flush` à la fin avec le paramètre de fermeture défini sur « true ».
> Pour obtenir un exemple détaillé de l’utilisation correcte du SDK Data Lake Gen2, consultez [Charger un fichier en utilisant le kit SDK Azure Data Lake](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk).
> * Quand le point de terminaison Event Hub n’accuse pas réception d’un événement, Azure Event Grid active un mécanisme de nouvelle tentative. Si cette nouvelle tentative de remise échoue, Event Grid peut délivrer les événements non remis à un compte de stockage en utilisant un processus de *mise en file d’attente de lettres mortes*. Pour plus d’informations, consultez la page [Distribution et nouvelle tentative de distribution de messages avec Event Grid](/azure/event-grid/delivery-and-retry#retry-schedule-and-duration).

## <a name="delete-blobs-using-storage-lifecycle"></a>Supprimer des objets blob à l’aide du cycle de vie du stockage

Azure Data Explorer ne supprimera pas les objets blob après l’ingestion. Pour gérer la suppression des objets blob, utilisez le [cycle de vie du stockage Blob Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal). Il est recommandé de conserver les objets blob pendant trois à cinq jours.

## <a name="known-event-grid-issues"></a>Problèmes connus liés à Event Grid

* Lorsque vous utilisez Azure Data Explorer pour [exporter](kusto/management/data-export/export-data-to-storage.md) les fichiers utilisés pour l’ingestion Event Grid, notez les éléments suivants : 
    * Les notifications Event Grid ne sont pas déclenchées si la chaîne de connexion fournie à la commande d’exportation ou à une [table externe](kusto/management/data-export/export-data-to-an-external-table.md) est une chaîne de connexion au [format ADLS Gen2](kusto/api/connection-strings/storage.md#azure-data-lake-store) (par exemple, `abfss://filesystem@accountname.dfs.core.windows.net`), mais que le compte de stockage n’est pas activé pour l’espace de noms hiérarchique.
    * Si le compte n’est pas activé pour l’espace de noms hiérarchique, la chaîne de connexion doit utiliser le format de [stockage Blob](kusto/api/connection-strings/storage.md#azure-storage-blob) (par exemple, `https://accountname.blob.core.windows.net`). L’exportation fonctionne comme prévu même si vous utilisez la chaîne de connexion ADLS Gen2, mais les notifications ne seront pas déclenchées et l’ingestion Event Grid ne fonctionnera pas.

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des objets blob dans Azure Data Explorer en s’abonnant à des notifications Event Grid](ingest-data-event-grid.md)
* [Créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de C#](data-connection-event-grid-csharp.md)
* [Créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de Python](data-connection-event-grid-python.md)
* [Créer une connexion de données Event Grid pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)
