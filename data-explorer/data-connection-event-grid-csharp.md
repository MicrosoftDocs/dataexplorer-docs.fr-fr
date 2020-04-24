---
title: Créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de C#
description: Dans cet article, vous allez apprendre à créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: d2432d302640d20bb199972bc686dadab41278f1
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493093"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Créer une connexion de données à Event Grid pour Azure Data Explorer à l’aide de C#

> [!div class="op_single_selector"]
> * [Portail](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)


L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Azure Data Explorer offre une ingestion (chargement de données) à partir de hubs d’événements, de hubs IoT et d’objets blob écrits dans des conteneurs d’objets blob. Dans cet article, vous créez une connexion de données à Event Grid pour Azure Data Explorer à l’aide de C#.

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas encore installé Visual Studio 2019, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.
* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créez [un cluster et une base de données](create-cluster-database-csharp.md).
* Créez [une table et un mappage de colonnes](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).
* Définissez [des stratégies de base de données et de table](database-table-policies-csharp.md) (facultatif).
* Créez un [compte de stockage avec un abonnement Event Grid](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

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

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
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

## <a name="generate-sample-data"></a>Générer un exemple de données

Maintenant qu’Azure Data Explorer et le compte de stockage sont connectés, vous pouvez créer des exemples de données et les charger dans le stockage d’objets blob.

Tout d’abord, le script crée un conteneur dans votre compte de stockage, télécharge un fichier existant (tel qu’objet blob) dans ce conteneur, puis affiche la liste des objets blob dans le conteneur.

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

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Azure Data Explorer ne supprimera pas les objets blob après l’ingestion. Conservez les blobs pendant trois à cinq jours en utilisant le [cycle de vie du Stockage Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) pour gérer la suppression des blobs.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
