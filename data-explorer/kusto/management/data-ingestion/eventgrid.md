---
title: Ingestion du stockage à l’aide de Event Grid abonnement-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’acquisition à partir du stockage à l’aide de Event Grid abonnement dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 43012752889d534d8f74943cfa6c528b2c72cd8b
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780641"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Ingérer à partir du stockage avec un abonnement Event Grid

Azure Explorateur de données offre une ingestion continue depuis le stockage Azure (stockage BLOB et ADLSv2) avec [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/overview) abonnement pour les notifications créées par un objet BLOB et en diffusant ces notifications à Kusto via un hub d’événements.

## <a name="data-format"></a>Format de données

* Les objets BLOB peuvent être dans n’importe quel [format pris en charge](../../../ingestion-supported-formats.md).
* Les objets BLOB peuvent être compressés dans l’une des [compressions prises en charge](../../../ingestion-supported-formats.md#supported-data-compression-formats)

> [!NOTE]
> Dans l’idéal, la taille des données non compressées d’origine doit faire partie des métadonnées de l’objet BLOB.
> Si la taille non compressée n’est pas spécifiée, Azure Explorateur de données l’évalue en fonction de la taille du fichier. Indiquez la taille des données d’origine en affectant à la `rawSizeBytes` [propriété](#ingestion-properties) sur les métadonnées de l’objet BLOB la valeur de données **non compressées** en octets.
> Notez qu’il existe une limite de taille décompressée pour la réception par fichier de 4 Go.

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Vous pouvez spécifier les [Propriétés](../../../ingestion-properties.md) d’ingestion de l’ingestion d’objets BLOB via les métadonnées de l’objet BLOB.
Vous pouvez définir les propriétés suivantes :

|Propriété | Description|
|---|---|
| rawSizeBytes | Taille des données brutes (non compressées). Pour Avro/ORC/parquet, cette valeur est la taille avant l’application de la compression spécifique au format.|
| kustoTable |  Nom de la table cible existante. Remplace le paramètre `Table` défini dans le panneau `Data Connection`. |
| kustoDataFormat |  Format de données. Remplace le paramètre `Data format` défini dans le panneau `Data Connection`. |
| kustoIngestionMappingReference |  Nom du mappage d’ingestion existant à utiliser. Remplace le paramètre `Column mapping` défini dans le panneau `Data Connection`.|
| kustoIgnoreFirstRecord | Si la valeur est `true` , Azure Explorateur de données ignore la première ligne de l’objet BLOB. Utilisez dans les données au format tabulaire (CSV, TSV ou similaire) pour ignorer les en-têtes. |
| kustoExtentTags | Chaîne représentant les [balises](../extents-overview.md#extent-tagging) qui seront attachées à l’étendue résultante. |
| kustoCreationTime |  Remplace [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) pour l’objet BLOB, au format de chaîne ISO 8601. À utiliser pour le renvoi. |

## <a name="events-routing"></a>Routage des événements

Quand vous configurez une connexion de stockage d’objets BLOB vers un cluster Azure Explorateur de données, spécifiez les propriétés de la table cible (nom de table, format de données et mappage). Ce programme d’installation est le routage par défaut de vos données, également appelé `static routing` .
Vous pouvez également spécifier des propriétés de la table cible pour chaque objet BLOB, à l’aide de métadonnées d’objet BLOB. Les données seront routées dynamiquement comme spécifié par les [Propriétés](#ingestion-properties)d’ingestion.

Voici un exemple de définition des propriétés d’ingestion des métadonnées d’objet BLOB avant de les charger. Les objets BLOB sont acheminés vers différentes tables.

Reportez-vous à l' [exemple de code](#generating-data) pour plus d’informations sur la façon de générer des données.

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
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Explorateur de données.

### <a name="prerequisites"></a>Prérequis

* [Créer un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
  Event Grid abonnement aux notifications peut être défini sur les comptes de stockage Azure de type `BlobStorage` ou `StorageV2` . 
  L’activation de [Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) est également prise en charge.
* [Créez un Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Abonnement Event Grid

* Kusto sélectionné `Event Hub` comme type de point de terminaison, utilisé pour le transport des notifications d’événements de stockage BLOB. `Event Grid schema`est le schéma sélectionné pour les notifications. Chaque concentrateur pair peut servir une seule connexion.
* La connexion de l’abonnement au stockage d’objets BLOB gère les notifications de type `Microsoft.Storage.BlobCreated` . Veillez à le sélectionner lors de la création de l’abonnement. Les autres types de notifications, s’ils sont sélectionnés, sont ignorés.
* Un abonnement peut notifier les événements de stockage dans un conteneur ou plus. Si vous souhaitez suivre des fichiers à partir d’un ou de plusieurs conteneurs spécifiques, définissez les filtres pour les notifications comme suit : lors de la configuration d’une connexion, prenez un soin particulier pour les valeurs suivantes : 
   * L' **objet commence par** le filtre est le préfixe *littéral* du conteneur d’objets BLOB. Comme le modèle appliqué est *startswith*, il peut s’étendre sur plusieurs conteneurs. Les caractères génériques ne sont pas autorisés.
     Elle *doit* être définie comme suit : *`/blobServices/default/containers/<prefix>`* . Par exemple : */blobServices/default/containers/StormEvents-2020-*
   * Le champ **Le sujet se termine par** est le suffixe *littéral* de l’objet blob. Les caractères génériques ne sont pas autorisés. Utile pour filtrer les extensions de fichier.

Une procédure détaillée est disponible dans le Guide de [création d’un abonnement Event Grid dans votre compte de stockage](../../../ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account) .

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Connexion d’ingestion de données à Azure Explorateur de données

* Via Portail Azure : [créez une connexion de données Event Grid dans Azure Explorateur de données](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).
* Utilisation du kit de développement logiciel (SDK) .NET Kusto Management : [Ajouter une connexion de données Event Grid](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Utilisation du kit de développement logiciel (SDK) python Management Kusto : [Ajouter une connexion de données Event Grid](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* Avec le modèle ARM : [Azure Resource Manager modèle pour l’ajout d’une connexion de données Event Grid](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>Génération des données

> [!NOTE]
> * Utilisez `BlockBlob` pour générer des données. La fonction `AppendBlob` n'est pas prise en charge.
<!--> * Using ADLSv2 storage requires using `CreateFile` API for uploading blobs and flush at the end. 
    Kusto will get 2 notificatiopns: when blob is created and when stream is flushed. First notification arrives before the data is ready and ignored. Second notification is processed.-->

Voici un exemple de création d’un objet blob à partir d’un fichier local, la définition des propriétés d’ingestion sur les métadonnées de l’objet BLOB et son téléchargement :

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

## <a name="blob-lifecycle"></a>Cycle de vie des objets BLOB

Azure Data Explorer ne supprimera pas les objets blob après l’ingestion. Utilisez le [cycle de vie du stockage d’objets BLOB Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) pour gérer la suppression de votre objet BLOB. Il est recommandé de conserver les objets BLOB pendant trois à cinq jours.

## <a name="known-issues"></a>Problèmes connus

Lorsque vous utilisez Azure Explorateur de données pour [Exporter](../data-export/export-data-to-storage.md) les fichiers utilisés pour l’ingestion de la grille d’événements, notez les éléments suivants : 
* Les notifications de Event Grid ne sont pas déclenchées si la chaîne de connexion fournie à la commande d’exportation ou la chaîne de connexion fournie à une [table externe](../data-export/export-data-to-an-external-table.md) est une chaîne de connexion au [format ADLS Gen2](../../api/connection-strings/storage.md#azure-data-lake-store)(par exemple `abfss://filesystem@accountname.dfs.core.windows.net` ), mais que le compte de stockage n’est pas activé pour l’espace de noms hiérarchique. 
* Si le compte n’est pas activé pour l’espace de noms hiérarchique, la chaîne de connexion doit utiliser le format de [stockage d’objets BLOB](../../api/connection-strings/storage.md#azure-storage-blob) (par exemple, `https://accountname.blob.core.windows.net` ). L’exportation fonctionne comme prévu même si vous utilisez la chaîne de connexion ADLS Gen2, mais les notifications ne seront pas déclenchées et Event Grid ingestion ne fonctionnera pas.
