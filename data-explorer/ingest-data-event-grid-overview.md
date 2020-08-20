---
title: Ingérer à partir du stockage avec un abonnement Event Grid - Azure Data Explorer
description: Cet article décrit l’ingestion à partir du stockage avec un abonnement Event Grid dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/13/2020
ms.openlocfilehash: e815a008c219c0eb1eeede8df07817d872ca1fed
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202405"
---
# <a name="connect-to-event-grid"></a>Se connecter à Event Grid

Event Grid est un pipeline qui écoute le stockage Azure et met à jour Azure Data Explorer pour extraire des informations à la suite d’événements associés à des abonnements. Azure Data Explorer offre une ingestion continue à partir du stockage Azure (stockage Blob et ADLSv2) avec un abonnement [Azure Event Grid](/azure/event-grid/overview) pour les notifications créées par un objet blob et le streaming de ces notifications vers Azure Data Explorer via un hub d’événements.

Le pipeline d’ingestion Event Grid passe par plusieurs étapes. Vous créez une table cible dans Azure Data Explorer dans laquelle les [données sous un format particulier](#data-format) sont ingérées. Vous créez ensuite une connexion de données Event Grid dans Azure Data Explorer. La connexion de données Event Grid doit connaître les informations de [routage des événements](#set-events-routing), telles que la table à laquelle envoyer les données et le mappage de table. Vous spécifiez également les [propriétés d’ingestion](#set-ingestion-properties), qui décrivent les données à ingérer, la table cible et le mappage. Ce processus peut être géré par le biais du [portail Azure](ingest-data-event-grid.md), par programmation avec [C#](data-connection-event-grid-csharp.md) ou [Python](data-connection-event-grid-python.md), ou avec le [modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md).

## <a name="data-format"></a>Format de données

* Examinez les [formats pris en charge](ingestion-supported-formats.md).
* Examinez les [compressions prises en charge](ingestion-supported-formats.md#supported-data-compression-formats).
  * La taille des données non compressées d’origine doit faire partie des métadonnées d’objets blob, sinon Azure Data Explorer l’estime.  La limite de taille décompressée d’ingestion par fichier est de 4 Go.
 
## <a name="set-ingestion-properties"></a>Définir les propriétés d’ingestion

Vous pouvez spécifier les [propriétés d’ingestion](ingestion-properties.md) de l’ingestion d’objets blob via les métadonnées d’objet blob.
Vous pouvez définir les propriétés suivantes :

[!INCLUDE [ingestion-properties-event-grid](includes/ingestion-properties-event-grid.md)]

## <a name="set-events-routing"></a>Définir le routage des événements

Quand vous configurez une connexion de stockage Blob vers un cluster Azure Data Explorer, spécifiez les propriétés de la table cible :
* Nom de la table
* format de données
* mapping

Cette configuration est le routage par défaut pour vos données, parfois appelé `static routing`.
Vous pouvez également spécifier des propriétés de la table cible pour chaque objet blob, à l’aide des métadonnées d’objet blob. Les données sont routées dynamiquement, comme spécifié par les [propriétés d’ingestion](#set-ingestion-properties).

L’exemple suivant montre comment définir les propriétés d’ingestion sur les métadonnées de l’objet blob avant de le charger. Les objets blob sont routés vers différentes tables.

Pour plus d’informations, consultez [Générer les données](#generate-data).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

### <a name="generate-data"></a>Générer les données

> [!NOTE]
> Utilisez `BlockBlob` pour générer les données. `AppendBlob` n’est pas pris en charge.

Vous pouvez créer un objet blob à partir d’un fichier local, définir des propriétés d’ingestion sur les métadonnées de l’objet blob, puis le charger comme suit :

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Create the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob metadata and upload the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

> [!NOTE]
> L’utilisation du SDK de stockage Azure Data Lake Gen2 requiert l’utilisation de `CreateFile` pour charger des fichiers et de `Flush` à la fin avec le paramètre de fermeture défini sur « true ».
> Pour obtenir un exemple détaillé de l’utilisation correcte du SDK Data Lake Gen2, consultez [Charger un fichier en utilisant le kit SDK Azure Data Lake](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk).

## <a name="delete-blobs-using-storage-lifecycle"></a>Supprimer des objets blob à l’aide du cycle de vie du stockage

Azure Data Explorer ne supprimera pas les objets blob après l’ingestion. Pour gérer la suppression des objets blob, utilisez le [cycle de vie du stockage Blob Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal). Il est recommandé de conserver les objets blob pendant trois à cinq jours.

## <a name="known-event-grid-issues"></a>Problèmes connus liés à Event Grid

* Lorsque vous utilisez Azure Data Explorer pour [exporter](kusto/management/data-export/export-data-to-storage.md) les fichiers utilisés pour l’ingestion Event Grid, notez les éléments suivants : 
    * Les notifications Event Grid ne sont pas déclenchées si la chaîne de connexion fournie à la commande d’exportation ou à une [table externe](kusto/management/data-export/export-data-to-an-external-table.md) est une chaîne de connexion au [format ADLS Gen2](kusto/api/connection-strings/storage.md#azure-data-lake-store) (par exemple, `abfss://filesystem@accountname.dfs.core.windows.net`), mais que le compte de stockage n’est pas activé pour l’espace de noms hiérarchique. 
    * Si le compte n’est pas activé pour l’espace de noms hiérarchique, la chaîne de connexion doit utiliser le format de [stockage Blob](kusto/api/connection-strings/storage.md#azure-storage-blob) (par exemple, `https://accountname.blob.core.windows.net`). L’exportation fonctionne comme prévu même si vous utilisez la chaîne de connexion ADLS Gen2, mais les notifications ne seront pas déclenchées et l’ingestion Event Grid ne fonctionnera pas.

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des objets blob dans Azure Data Explorer en s’abonnant à des notifications Event Grid](ingest-data-event-grid.md)
* [Créer une connexion de données au hub d’événements pour Azure Data Explorer à l’aide de C#](data-connection-event-hub-csharp.md)
* [Créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de Python](data-connection-event-grid-python.md)
* [Créer une connexion de données Event Grid pour Azure Data Explorer à l’aide d’un modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)
