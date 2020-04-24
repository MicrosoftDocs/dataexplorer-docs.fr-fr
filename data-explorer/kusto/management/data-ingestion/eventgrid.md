---
title: Ingest de stockage à l’aide de l’abonnement Event Grid - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Ingest depuis le stockage à l’aide de l’abonnement Event Grid dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 682c39b11292c7e198dba46dad9b5bfa3b520c0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521520"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Ingest de stockage en utilisant l’abonnement Event Grid

Azure Data Explorer offre une ingestion continue à partir d’Azure Storage (Stockage Blob et ADLSv2) avec [l’abonnement Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) pour les notifications créées par blob et la diffusion de ces notifications sur Kusto via un Event Hub.

## <a name="data-format"></a>Format de données

* Blobs peut être dans l’un des [formats pris en charge](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).
* Les blobs peuvent être comprimés dans n’importe laquelle des [compressions soutenues](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats#supported-data-compression-formats)

> [!NOTE]
> Idéalement, la taille des données non compressées d’origine devrait faire partie des métadonnées blob.
> Si la taille non compressée n’est pas spécifiée, Azure Data Explorer l’estimera en fonction de la taille du fichier. Fournir la taille des `rawSizeBytes` données d’origine en définissant la [propriété](#ingestion-properties) sur les métadonnées blob à la taille des données **non compressées** dans les octets.
> Veuillez noter qu’il y a une limite de taille non compressée par fichier de 4 Go.

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Vous pouvez spécifier [les propriétés ingestion](https://docs.microsoft.com/azure/data-explorer/ingestion-properties) de l’ingestion blob via les métadonnées blob.
Vous pouvez définir les propriétés suivantes :

|Propriété | Description|
|---|---|
| rawSizeBytes | Taille des données brutes (non compressées). Pour Avro/ORC/Parquet, c’est la taille avant l’application d’une compression spécifique au format.|
| kustoTable (en) |  Nom de la table cible existante. Remplace l’ensemble `Table` sur `Data Connection` la lame. |
| kustoDataFormat kustoDataFormat |  Format de données. Remplace l’ensemble `Data format` sur `Data Connection` la lame. |
| kustoIngestionMappingReference |  Nom de la cartographie d’ingestion existante à utiliser. Remplace l’ensemble `Column mapping` sur `Data Connection` la lame.|
| kustoIgnoreFirstRecord | Si défini `true`à , Azure Data Explorer ignore la première rangée de l’objet blob. Utilisez dans les données de format tabulaire (CSV, TSV, ou similaires) pour ignorer les en-têtes. |
| kustoExtentTags | Chaîne représentant [les étiquettes](../extents-overview.md#extent-tagging) qui seront attachées dans l’étendue résultante. |
| kustoCréationTime (en anglais) |  Remplace [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) pour le blob, formaté comme une chaîne ISO 8601. Utiliser pour le remblayage. |

## <a name="events-routing"></a>Itinéraire d’événements

Lors de la configuration d’une connexion de stockage blob au cluster Azure Data Explorer, spécifiez les propriétés du tableau cible (nom de table, format de données et cartographie). Il s’agit de l’itinéraire par défaut `static routig`pour vos données, également appelés .
Vous pouvez également spécifier les propriétés de table cible pour chaque blob, à l’aide de métadonnées blob. Les données seront routées dynamiquement comme spécifié par les [propriétés d’ingestion.](#ingestion-properties)

Voici un exemple pour définir des propriétés d’ingestion aux métadonnées blob avant de les télécharger. Blobs sont acheminés vers différentes tables.

Veuillez consulter le [code de l’échantillon](#generating-data) pour plus de détails sur la façon de générer des données.

 ```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-event-grid-subscription"></a>Créer un abonnement Event Grid

> [!Note]
> Pour de meilleures performances, créez toutes les ressources dans la même région que le cluster Azure Data Explorer.

### <a name="prerequisites"></a>Prérequis

* [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
  L’abonnement à la notification Event Grid `BlobStorage` peut `StorageV2`être défini sur les comptes de stockage Azure de nature ou . 
  Enabling [Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) est également pris en charge.
* [Créez un hub d’événements](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Abonnement Event Grid

* Kusto `Event Hub` sélectionné comme type endpoind, utilisé pour le transport des notifications d’événements de stockage blob. `Event Grid schema`est le schéma choisi pour les notifications. Notez que chaque Hub Even peut servir une connexion.
* La connexion d’abonnement de stockage `Microsoft.Storage.BlobCreated`blob gère les notifications de type . Assurez-vous de le sélectionner lors de la création de l’abonnement. Notez que d’autres types de notifications, s’ils sont sélectionnés, sont ignorés.
* Un abonnement peut en informer sur les événements de stockage dans un conteneur ou plus. Si vous souhaitez suivre les fichiers à partir d’un conteneur spécifique, définissez les filtres pour les notifications comme suit : Lors de la configuration d’une connexion, prenez soin des valeurs suivantes : 
   * **Sujet commence avec** le filtre est le préfixe *littéral* du récipient blob. Comme le modèle appliqué est *startswith*, il peut s’étendre sur plusieurs conteneurs. Les caractères génériques ne sont pas autorisés.
     Il *doit* être défini *`/blobServices/default/containers/<prefix>`* comme suit: . Par exemple : */blobServices/default/containers/StormEvents-2020-*
   * Le champ **Le sujet se termine par** est le suffixe *littéral* de l’objet blob. Les caractères génériques ne sont pas autorisés. Utile pour filtrer les extensions de fichiers.

Une visite détaillée peut être trouvée dans le comment créer un abonnement Event Grid dans votre guide [de compte de stockage.](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-subscription-in-your-storage-account)

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Connexion d’ingestion de données à Azure Data Explorer

* Via Azure Portal: [Créer une connexion de données Event Grid dans Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-grid#create-an-event-grid-data-connection-in-azure-data-explorer).
* Utilisation de la gestion Kusto .NET SDK: [Ajouter une connexion de données Event Grid](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-csharp#add-an-event-grid-data-connection)
* Utilisation de la gestion Kusto Python SDK: [Ajouter une connexion de données Event Grid](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-python#add-an-event-grid-data-connection)
* Avec le modèle ARM : [modèle Azure Resource Manager pour l’ajout d’une connexion de données Event Grid](https://docs.microsoft.com/azure/data-explorer/data-connection-event-grid-resource-manager#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>Génération de données

> [!NOTE]
> * Utiliser `BlockBlob` pour générer des données. La fonction `AppendBlob` n'est pas prise en charge.
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

Voici un exemple pour créer un blob à partir de fichier local, en définissant les propriétés d’ingestion aux métadonnées blob et en le téléchargeant :

 ```csharp
 var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set ingestion properties in blob's metadata & uploading the blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIgnoreFirstRecord", "true"); // First line of this csv file are headers
blob.UploadFromFile(csvCompressedLocalFileName);
```

## <a name="blob-lifecycle"></a>Cycle de vie Blob

Azure Data Explorer ne supprimera pas l’ingestion de poteau de blobs, mais les conservera pendant trois à cinq jours. Utilisez [le cycle de vie de stockage Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) pour gérer votre suppression de blob.