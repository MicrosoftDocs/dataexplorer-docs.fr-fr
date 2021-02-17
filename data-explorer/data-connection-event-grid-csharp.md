---
title: Créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de C#
description: Dans cet article, vous allez apprendre à créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de C#.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: 3c79d95575e88e7f4d3969ad0cf521703db536e9
ms.sourcegitcommit: c11e3871d600ecaa2824ad78bce9c8fc5226eef9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99554694"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de C#

> [!div class="op_single_selector"]
> * [Portail](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)


[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
 Dans cet article, vous créez une connexion de données à Event Grid pour Azure Data Explorer à l’aide de C#.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas encore installé Visual Studio 2019, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.
* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créez [un cluster et une base de données](create-cluster-database-csharp.md).
* Créez [une table et un mappage de colonnes](./net-sdk-ingest-data.md#create-a-table-on-your-test-cluster).
* Définissez [des stratégies de base de données et de table](database-table-policies-csharp.md) (facultatif).
* Créez un [compte de stockage avec un abonnement Event Grid](ingest-data-event-grid.md).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Ajouter une connexion de données à Event Grid

L’exemple suivant montre comment ajouter par programmation une connexion de données à Event Grid. Consultez [Créer une connexion de données à Event Grid dans Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) pour ajouter une connexion de données à Event Grid à l’aide du Portail Azure.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
var blobStorageEventType = "Microsoft.Storage.BlobCreated";

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat, blobStorageEventType: blobStorageEventType));
```

|**Paramètre** | **Valeur suggérée** | **Description du champ**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Votre ID de client. Également appelé ID de répertoire.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID d’abonnement que vous utilisez pour la création de ressources.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| clientSecret | *xxxxxxxxxxxxxx* | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire. |
| resourceGroupName | *testrg* | Nom du groupe de ressources qui contient votre cluster.|
| clusterName | *mykustocluster* | Nom de votre cluster.|
| databaseName | *mykustodatabase* | Nom de la base de données cible dans votre cluster.|
| dataConnectionName | *myeventhubconnect* | Nom souhaité de votre connexion de données.|
| tableName | *StormEvents* | Nom de la table cible dans la base de données cible.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Nom de votre mappage de colonnes associé à la table cible.|
| dataFormat | *csv* | Format de données du message.|
| eventHubResourceId | *ID de ressource* | ID de ressource de votre hub d’événements où la grille d’événements est configurée pour envoyer des événements. |
| storageAccountResourceId | *ID de ressource* | ID de ressource de votre compte de stockage qui contient les données à des fins d’ingestion. |
| consumerGroup | *$Default* | Groupe de consommateurs de votre hub d’événements.|
| location | *USA Centre* | Emplacement de la ressource de connexion de données.|
| blobStorageEventType | *Microsoft.Storage.BlobCreated* | Type d’événement qui déclenche l’ingestion. Les événements pris en charge sont les suivants : Microsoft.Storage.BlobCreated ou Microsoft.Storage.BlobRenamed. Le renommage d’objets blob est pris en charge uniquement pour le stockage ADLSv2.|

## <a name="generate-sample-data"></a>Générer un exemple de données

Maintenant qu’Azure Data Explorer et le compte de stockage sont connectés, vous pouvez créer un exemple de données et les charger dans le stockage.

> [!NOTE]
> Azure Data Explorer ne supprimera pas les objets blob après l’ingestion. Conservez les blobs pendant trois à cinq jours en utilisant le [cycle de vie du Stockage Blob Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) pour gérer la suppression des blobs.

### <a name="upload-file-using-azure-blob-storage-sdk"></a>Charger un fichier en utilisant le kit SDK Stockage Blob Azure

L’extrait de code suivant crée un conteneur dans votre compte de stockage, charge un fichier existant (tel qu’objet blob) dans ce conteneur, puis affiche la liste des objets blob dans le conteneur.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName = <container_name>;
var blobName = <blob_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mappingReference>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
blob.Metadata.Add("kustoIngestionMappingReference", mapping);
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

### <a name="upload-file-using-azure-data-lake-sdk"></a>Charger un fichier en utilisant le kit SDK Azure Data Lake

Lorsque vous utilisez Data Lake Storage Gen2, vous pouvez utiliser le [SDK Azure Data Lake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) pour charger des fichiers dans le stockage. L’extrait de code suivant utilise Azure.Storage.Files.DataLake v12.5.0 pour créer un système de fichiers dans le stockage Azure Data Lake et charger un fichier local avec les métadonnées sur ce système de fichiers.

```csharp
var accountName = <storage_account_name>;
var accountKey = <storage_account_key>;
var fileSystemName = <file_system_name>;
var fileName = <file_name>;
var localFileName = <file_to_upload>;
var uncompressedSizeInBytes = <uncompressed_size_in_bytes>;
var mapping = <mapping_reference>;

var sharedKeyCredential = new StorageSharedKeyCredential(accountName, accountKey);
var dfsUri = "https://" + accountName + ".dfs.core.windows.net";
var dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), sharedKeyCredential);

// Create the filesystem
var dataLakeFileSystemClient = dataLakeServiceClient.CreateFileSystem(fileSystemName).Value;

// Define metadata
IDictionary<String, String> metadata = new Dictionary<string, string>();
metadata.Add("rawSizeBytes", uncompressedSizeInBytes);
metadata.Add("kustoIngestionMappingReference", mapping);

// Set uploading options
var uploadOptions = new DataLakeFileUploadOptions
{
    Metadata = metadata,
    Close = true // Note: The close option triggers the event being processed by the data connection
};

// Write to the file
var dataLakeFileClient = dataLakeFileSystemClient.GetFileClient(fileName);
dataLakeFileClient.Upload(localFileName, uploadOptions);
```

> [!NOTE]
> Quand vous utilisez le [SDK Azure Data Lake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) pour charger un fichier, la création du fichier déclenche un événement Event Grid avec la taille 0 et cet événement est ignoré par Azure Data Explorer. Le vidage de fichier déclenche un autre événement si le paramètre *Close* a la valeur *true*. Cet événement indique qu’il s’agit de la mise à jour finale et que le flux de fichier a été fermé. Cet événement est traité par la connexion de données Event Grid. Dans l’extrait de code ci-dessus, la méthode Upload déclenche un vidage lorsque le chargement de fichier est terminé. Par conséquent, un paramètre *Close* avec la valeur *true* doit être défini. Pour plus d’informations sur le vidage, consultez [Méthode de vidage Azure Data Lake](/dotnet/api/azure.storage.files.datalake.datalakefileclient.flush).

### <a name="rename-file-using-azure-data-lake-sdk"></a>Renommer un fichier en utilisant le kit SDK Azure Data Lake

L’extrait de code suivant utilise le [SDK Azure Data Lake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) pour renommer un objet blob dans un compte de stockage ADLSv2.

```csharp
var accountName = <storage_account_name>;
var accountKey = <storage_account_key>;
var fileSystemName = <file_system_name>;
var sourceFilePath = <source_file_path>;
var destinationFilePath = <destination_file_path>;

var sharedKeyCredential = new StorageSharedKeyCredential(accountName, accountKey);
var dfsUri = "https://" + accountName + ".dfs.core.windows.net";
var dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), sharedKeyCredential);

// Get a client to the the filesystem
var dataLakeFileSystemClient = dataLakeServiceClient.GetFileSystemClient(fileSystemName);

// Rename a file in the file system
var dataLakeFileClient = dataLakeFileSystemClient.GetFileClient(sourceFilePath);
dataLakeFileClient.Rename(destinationFilePath);
```

> [!NOTE]
> * Le renommage de répertoire est possible dans ADLSv2, mais ne déclenche pas d’événements de *renommage d’objets blob* ni l’ingestion d’objets blob dans le répertoire. Pour ingérer les objets blob suite au renommage, renommez directement les objets blob souhaités.
> * Si vous avez défini des filtres pour suivre des sujets spécifiques pendant la [création de la connexion de données](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) ou lors de la création manuelle des [ressources Event Grid](ingest-data-event-grid-manual.md#create-an-event-grid-subscription), ces filtres sont appliqués sur le chemin du fichier de destination.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
