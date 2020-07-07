---
title: Ingestion du stockage à l’aide de Event Grid abonnement-Azure Explorateur de données
description: Cet article décrit l’acquisition à partir du stockage à l’aide de Event Grid abonnement dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 07/01/2020
ms.openlocfilehash: 3a69add7e395bbb5b18c390c4089a2e8ad80f674
ms.sourcegitcommit: 0d15903613ad6466d49888ea4dff7bab32dc5b23
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "86013787"
---
# <a name="ingest-from-storage-using-event-grid-subscription"></a>Ingérer à partir du stockage avec un abonnement Event Grid

Azure Explorateur de données offre une ingestion continue à partir du stockage Azure (stockage BLOB et ADLSv2) avec [Azure Event Grid](/azure/event-grid/overview) abonnement pour les notifications créées par un objet BLOB et en diffusant ces notifications vers Azure Explorateur de données via un hub d’événements.

## <a name="data-format"></a>Format de données

* Les objets BLOB peuvent être dans n’importe quel [format pris en charge](../../../ingestion-supported-formats.md).
* Les objets BLOB peuvent être compressés. Pour plus d’informations, consultez [compressions prises en charge](../../../ingestion-supported-formats.md#supported-data-compression-formats).

> [!NOTE]
> Dans l’idéal, la taille des données non compressées d’origine doit faire partie des métadonnées de l’objet BLOB.
> Si la taille non compressée n’est pas spécifiée, Azure Explorateur de données l’évalue en fonction de la taille du fichier. Vous pouvez fournir la taille des données d’origine en affectant à la `rawSizeBytes` [propriété](#ingestion-properties) sur les métadonnées de l’objet BLOB la valeur de données non compressées en octets.
> 
> Il existe une limite de taille non compressée d’ingestion par fichier de 4 Go.

## <a name="ingestion-properties"></a>Propriétés d’ingestion

Vous pouvez spécifier les [Propriétés](../../../ingestion-properties.md) d’ingestion de l’ingestion d’objets BLOB via les métadonnées de l’objet BLOB.
Vous pouvez définir les propriétés suivantes :

|Propriété | Description|
|---|---|
| rawSizeBytes | Taille des données brutes (non compressées). Pour Avro/ORC/parquet, cette valeur est la taille avant l’application de la compression spécifique au format.|
| kustoTable |  Nom de la table cible existante. Remplace le paramètre `Table` défini dans le panneau `Data Connection`. |
| kustoDataFormat |  Format de données. Remplace le format de **données** défini dans le panneau **connexion de données** . |
| kustoIngestionMappingReference |  Nom du mappage d’ingestion existant à utiliser. Remplace le mappage de **colonnes** défini dans le panneau de **connexion de données** .|
| kustoIgnoreFirstRecord | Si la valeur est `true` , Azure Explorateur de données ignore la première ligne de l’objet BLOB. Utilisez dans les données au format tabulaire (CSV, TSV ou similaire) pour ignorer les en-têtes. |
| kustoExtentTags | Chaîne représentant les [balises](../extents-overview.md#extent-tagging) qui seront attachées à l’étendue résultante. |
| kustoCreationTime |  Remplace [$IngestionTime](../../query/ingestiontimefunction.md?pivots=azuredataexplorer) pour l’objet BLOB, au format de chaîne ISO 8601. À utiliser pour le renvoi. |

## <a name="events-routing"></a>Routage des événements

Quand vous configurez une connexion de stockage d’objets BLOB vers un cluster Azure Explorateur de données, spécifiez les propriétés de la table cible :
* Nom de la table
* format de données
* mapping

Ce programme d’installation est le routage par défaut de vos données, parfois appelé `static routing` .
Vous pouvez également spécifier des propriétés de la table cible pour chaque objet BLOB, à l’aide de métadonnées d’objet BLOB. Les données sont acheminées dynamiquement, comme spécifié par les [Propriétés](#ingestion-properties)d’ingestion.

Voici un exemple de définition des propriétés d’ingestion des métadonnées d’objet BLOB avant de les charger. Les objets BLOB sont acheminés vers différentes tables.

Pour plus d’informations sur la façon de générer des données, consultez [exemple de code](#generating-data).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Créer un abonnement Event Grid dans votre compte de stockage

> [!NOTE]
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Explorateur de données.

### <a name="prerequisites"></a>Prérequis

* [Créer un compte de stockage](/azure/storage/common/storage-quickstart-create-account).
  Event Grid abonnement aux notifications peut être défini sur les comptes de stockage Azure pour le type `BlobStorage` ou `StorageV2` .
  L’activation de [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction) est également prise en charge.
* [Créez un Event Hub](/azure/event-hubs/event-hubs-create).

### <a name="event-grid-subscription"></a>Abonnement Event Grid
 
1. Dans le portail Azure, recherchez votre compte de stockage.
1. Dans le menu de gauche, sélectionnez **événements événements**  >  **abonnement**.

     :::image type="content" source="../images/eventgrid/create-event-grid-subscription-1.png" alt-text="Créer un abonnement Event Grid":::

1. Dans la fenêtre **Créer un abonnement aux événements** sous l’onglet **De base**, spécifiez les valeurs suivantes :

    :::image type="content" source="../images/eventgrid/create-event-grid-subscription-2.png" alt-text="Créer des valeurs d’abonnement d’événement à entrer":::

    |**Paramètre** | **Valeur suggérée** | **Description du champ**|
    |---|---|---|
    | Nom | *test-grid-connection* | Nom de l’abonnement Event Grid que vous souhaitez créer.|
    | Schéma d’événement | *Schéma Event Grid* | Schéma à utiliser pour la grille d’événement. |
    | Type de rubrique | *Compte de stockage* | Type de rubrique Event Grid. |
    | Ressource source | *gridteststorage1* | nom de votre compte de stockage. |
    | Nom de la rubrique système | *gridteststorage1...* | Rubrique système dans laquelle Azure Storage publie des événements. Cette rubrique système transfère ensuite l’événement à un abonné qui reçoit et traite des événements. |
    | Filtrer sur les types d’événements | *Objet BLOB créé* | De quels événements spécifiques être notifié. Le type actuellement pris en charge est Microsoft. Storage. BlobCreated. Veillez à le sélectionner lors de la création de l’abonnement.|
    | Type de point de terminaison | *Hubs d'événements* | Type de point de terminaison auquel vous envoyez les événements. |
    | Point de terminaison | *test-hub* | Hub d’événements que vous avez créé. |

1. Sélectionnez l’onglet **filtres** si vous souhaitez suivre des sujets spécifiques. Définissez les filtres pour les notifications comme suit :
   * Sélectionnez **Activer le filtrage d’objet**.
   * Le champ **Subject Begin with** est le préfixe *littéral* de l’objet. Étant donné que le modèle appliqué est *StartsWith*, il peut s’étendre sur plusieurs conteneurs, dossiers ou objets BLOB. Les caractères génériques ne sont pas autorisés.
       * Pour définir un filtre sur le conteneur d’objets blob, le champ *doit* être défini comme suit : *`/blobServices/default/containers/[container prefix]`* .
       * Pour définir un filtre sur un préfixe d’objet BLOB (ou un dossier dans Azure Data Lake Gen), le champ *doit* être défini comme suit : *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
   * Le champ **Le sujet se termine par** est le suffixe *littéral* de l’objet blob. Les caractères génériques ne sont pas autorisés.
   * Le champ **de correspondance d’objet qui respecte la casse** indique si les filtres de préfixe et de suffixe respectent la casse.
   * Pour plus d’informations sur le filtrage des événements, consultez [Événements du stockage Blob](/azure/storage/blobs/storage-blob-event-overview#filtering-events).
    
        :::image type="content" source="../images/eventgrid/filters-tab.png" alt-text="Onglet Filtres-grille d’événements":::

### <a name="data-ingestion-connection-to-azure-data-explorer"></a>Connexion d’ingestion de données à Azure Explorateur de données

* Via Portail Azure : [créez une connexion de données Event Grid dans Azure Explorateur de données](../../../ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).
* Utilisation du kit de développement logiciel (SDK) .NET Kusto Management : [Ajouter une connexion de données Event Grid](../../../data-connection-event-grid-csharp.md#add-an-event-grid-data-connection)
* Utilisation du kit de développement logiciel (SDK) python Management Kusto : [Ajouter une connexion de données Event Grid](../../../data-connection-event-grid-python.md#add-an-event-grid-data-connection)
* Avec [Azure Resource Manager modèle pour l’ajout d’une connexion de données Event Grid](../../../data-connection-event-grid-resource-manager.md#azure-resource-manager-template-for-adding-an-event-grid-data-connection)

### <a name="generating-data"></a>Génération des données

> [!NOTE]
> * Utilisez `BlockBlob` pour générer des données. La fonction `AppendBlob` n'est pas prise en charge.

Voici un exemple de création d’un objet blob à partir d’un fichier local, de la définition des propriétés d’ingestion sur les métadonnées de l’objet BLOB et de son téléchargement :

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

> [!NOTE]
> L’utilisation de Azure Data Lake Kit de développement logiciel (SDK) stockage GEN requiert l’utilisation `CreateFile` de pour télécharger des fichiers et `Flush` à la fin avec le paramètre Close défini sur « true ».
> Pour obtenir un exemple détaillé de l’utilisation correcte du kit de développement logiciel (SDK) Data Lake GEN, consultez [Télécharger un fichier à l’aide de Azure Data Lake SDK](../../../data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk).

## <a name="blob-lifecycle"></a>Cycle de vie des objets BLOB

Azure Explorateur de données ne supprime pas les objets BLOB après réception. Utilisez le [cycle de vie du stockage d’objets BLOB Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) pour gérer la suppression de votre objet BLOB. Il est recommandé de conserver les objets BLOB pendant trois à cinq jours.

## <a name="known-issues"></a>Problèmes connus

* Lorsque vous utilisez Azure Explorateur de données pour [Exporter](../data-export/export-data-to-storage.md) les fichiers utilisés pour l’ingestion de la grille d’événements, notez les éléments suivants : 
    * Les notifications de Event Grid ne sont pas déclenchées si la chaîne de connexion fournie à la commande d’exportation ou la chaîne de connexion fournie à une [table externe](../data-export/export-data-to-an-external-table.md) est une chaîne de connexion au [format ADLS Gen2](../../api/connection-strings/storage.md#azure-data-lake-store) (par exemple `abfss://filesystem@accountname.dfs.core.windows.net` ), mais que le compte de stockage n’est pas activé pour l’espace de noms hiérarchique. 
    * Si le compte n’est pas activé pour l’espace de noms hiérarchique, la chaîne de connexion doit utiliser le format de [stockage d’objets BLOB](../../api/connection-strings/storage.md#azure-storage-blob) (par exemple, `https://accountname.blob.core.windows.net` ). L’exportation fonctionne comme prévu même si vous utilisez la chaîne de connexion ADLS Gen2, mais les notifications ne seront pas déclenchées et Event Grid ingestion ne fonctionnera pas.
