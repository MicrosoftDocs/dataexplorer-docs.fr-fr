---
title: Ingestion d’objets blob de bout en bout dans Azure Data Explorer par le biais de C#
description: Dans cet article, vous allez apprendre à ingérer des objets blob dans Azure Data Explorer à l’aide d’un exemple de bout en bout qui utilise C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8df2202d3a127c58cee90353a628bf933ea30a10
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493537"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-c"></a>Ingestion d’objets blob de bout en bout dans Azure Data Explorer par le biais de C#

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Data Explorer est un service d’exploration de données rapide et évolutif pour les données de journal et de télémétrie. Cet article vous donne un exemple de bout en bout sur la façon d’ingérer des données à partir de Stockage Blob Azure dans Azure Data Explorer. 

Vous allez apprendre à créer par programmation un groupe de ressources, un compte de stockage et un conteneur, un Event Hub et un cluster et une base de données Azure Data Explorer. Vous apprendrez également à configurer Azure Data Explorer de manière programmatique pour ingérer des données à partir du nouveau compte de stockage.

## <a name="prerequisites"></a>Prérequis

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="install-c-nuget"></a>Installer le package NuGet C#

* Installez [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installez [Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
* Installez [Microsoft.Azure.Management.EventGrid](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/).
* Installez [Microsoft.Azure.Storage.Blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/).
* Installez [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) à des fins d’authentification.

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Exemple de code 

L’exemple de code suivant fournit un processus pas à pas qui entraîne l’ingestion de données dans Azure Data Explorer. 

Commencez par créer un groupe de ressources. Vous pouvez également créer des ressources Azure, telles qu’un compte de stockage et un conteneur, un Event Hub et un cluster et une base de données Azure Data Explorer, et ajouter des principaux. Ensuite, créez un abonnement Azure Event Grid, ainsi qu’un mappage de table et de colonne, dans la base de données Azure Data Explorer. Enfin, créez la connexion de données afin de configurer Azure Data Explorer pour ingérer des données à partir du nouveau compte de stockage. 

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
string location = "West Europe";
string locationSmallCase = "westeurope";
string azureResourceTemplatePath = @"xxxxxxxxx\template.json";//Path to the Azure Resource Manager template JSON from the previous section

string deploymentName = "e2eexample";
string resourceGroupName = deploymentName + "resourcegroup";
string eventHubName = deploymentName + "eventhub";
string eventHubNamespaceName = eventHubName + "ns";
string storageAccountName = deploymentName + "storage";
string storageContainerName = deploymentName + "storagecontainer";
string eventGridSubscriptionName = deploymentName + "eventgrid";
string kustoClusterName = deploymentName + "kustocluster";
string kustoDatabaseName = deploymentName + "kustodatabase";
string kustoTableName = "Events";
string kustoColumnMappingName = "Events_CSV_Mapping";
string kustoDataConnectionName = deploymentName + "kustoeventgridconnection";

//principals
string principalIdForCluster = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
string roleForClusterPrincipal = "AllDatabasesAdmin";
string tenantIdForClusterPrincipal = tenantId;
string principalTypeForCluster = "App";
string principalIdForDatabase = "xxxxxxxx@xxxxxxxx.com";//User Email
string roleForDatabasePrincipal = "Admin";
string tenantIdForDatabasePrincipal = tenantId;
string principalTypeForDatabase = "User";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);
Console.WriteLine("Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.");
resourceManagementClient.SubscriptionId = subscriptionId;
await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(resourceGroupName,
    new ResourceGroup() { Location = locationSmallCase });

Console.WriteLine(
    "Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, database, and add principals by using an Azure Resource Manager template.");
var parameters = new Dictionary<string, Dictionary<string, object>>();
parameters["eventHubNamespaceName"] = new Dictionary<string, object>(capacity: 1) {{"value", eventHubNamespaceName}};
parameters["eventHubName"] = new Dictionary<string, object>(capacity: 1) {{"value", eventHubName }};
parameters["storageAccountName"] = new Dictionary<string, object>(capacity: 1) {{"value", storageAccountName }};
parameters["containerName"] = new Dictionary<string, object>(capacity: 1) {{"value", storageContainerName }};
parameters["kustoClusterName"] = new Dictionary<string, object>(capacity: 1) {{"value", kustoClusterName }};
parameters["kustoDatabaseName"] = new Dictionary<string, object>(capacity: 1) {{"value", kustoDatabaseName }};
parameters["principalIdForCluster"] = new Dictionary<string, object>(capacity: 1) {{"value", principalIdForCluster }};
parameters["roleForClusterPrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", roleForClusterPrincipal }};
parameters["tenantIdForClusterPrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", tenantIdForClusterPrincipal }};
parameters["principalTypeForCluster"] = new Dictionary<string, object>(capacity: 1) {{"value", principalTypeForCluster }};
parameters["principalIdForDatabase"] = new Dictionary<string, object>(capacity: 1) {{"value", principalIdForDatabase }};
parameters["roleForDatabasePrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", roleForDatabasePrincipal }};
parameters["tenantIdForDatabasePrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", tenantIdForDatabasePrincipal }};
parameters["principalTypeForDatabase"] = new Dictionary<string, object>(capacity: 1) {{"value", principalTypeForDatabase }};
            
string template = File.ReadAllText(azureResourceTemplatePath, Encoding.UTF8);
await resourceManagementClient.Deployments.CreateOrUpdateAsync(resourceGroupName, deploymentName,
    new Deployment(new DeploymentProperties(DeploymentMode.Incremental, template: template,
        parameters: parameters)));

Console.WriteLine(
    "Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.");
var eventGridClient = new EventGridManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
string storageResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
string eventHubResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.EventHub/namespaces/{eventHubNamespaceName}/eventhubs/{eventHubName}";
await eventGridClient.EventSubscriptions.CreateOrUpdateAsync(storageResourceId, eventGridSubscriptionName,
    new EventSubscription()
    {
        Destination = new EventHubEventSubscriptionDestination(eventHubResourceId),
        Filter = new EventSubscriptionFilter()
        {
            SubjectBeginsWith = $"/blobServices/default/containers/{storageContainerName}",
            IncludedEventTypes = new List<string>(){"Microsoft.Storage.BlobCreated"}
        }
    });

Console.WriteLine("Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.");
var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
{
    InitialCatalog = kustoDatabaseName,
    FederatedSecurity = true,
    ApplicationClientId = clientId,
    ApplicationKey = clientSecret,
    Authority = tenantId
};

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            kustoTableName,
            new[]
            {
                Tuple.Create("EventTime", "System.DateTime"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("EventSummary", "System.String"),
            });

    kustoClient.ExecuteControlCommand(command);

    command = CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
        kustoTableName,
        kustoColumnMappingName,
        new[]
        {
            new CsvColumnMapping { ColumnName = "EventTime", CslDataType="dateTime", Ordinal = 0 },
            new CsvColumnMapping { ColumnName = "EventId", CslDataType="int", Ordinal = 1 },
            new CsvColumnMapping { ColumnName = "EventSummary", CslDataType="string", Ordinal = 2 },
        });
    kustoClient.ExecuteControlCommand(command);
}

Console.WriteLine("Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.");
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, kustoClusterName,
                kustoDatabaseName, dataConnectionName: kustoDataConnectionName, new EventGridDataConnection(storageResourceId, eventHubResourceId, consumerGroup: "$Default", location: location, tableName:kustoTableName, mappingRuleName: kustoColumnMappingName, dataFormat: "csv"));

```
| **Paramètre** | **Description du champ** |
|---|---|---|
| tenantId | Votre ID de client. Il est également appelé ID de répertoire.|
| subscriptionId | ID d’abonnement que vous utilisez pour la création de ressources.|
| clientId | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| clientSecret | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire. |

## <a name="test-the-code-example"></a>Tester l’exemple de code

1. Chargez un fichier dans le compte de stockage.

    ```csharp
    string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=xxxxxxxxxxxxxx;AccountKey=xxxxxxxxxxxxxx;EndpointSuffix=core.windows.net";
    var cloudStorageAccount = CloudStorageAccount.Parse(storageConnectionString);
    CloudBlobClient blobClient = cloudStorageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("test.csv");
    var blobContent = @"2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm";
    await blockBlob.UploadTextAsync(blobContent);
    ```
    |**Paramètre** | **Description du champ**|
    |---|---|---|
    | storageConnectionString | Chaîne de connexion du compte de stockage créé de manière programmatique.|

2. Exécutez une requête de test dans Azure Data Explorer.

    ```csharp
    var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
    var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
    {
        InitialCatalog = kustoDatabaseName,
        FederatedSecurity = true,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };
    using (var kustoClient = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
    {
        var query = $"{kustoTableName} | take 10";
        using (var reader = kustoClient.ExecuteQuery(query) as DataTableReader2)
        {// Print the contents of each of the result sets. 
            while (reader.Read())
            {
                Console.WriteLine($"{reader[0]}, {reader[1]}, {reader[2]}");
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer le groupe de ressources et les ressources de nettoyage, exécutez la commande suivante :

```csharp
await resourceManagementClient.ResourceGroups.DeleteAsync(resourceGroupName);
```

## <a name="next-steps"></a>Étapes suivantes

*  Pour en savoir plus sur les autres méthodes de création d’un cluster et d’une base de données, voir [Créez un cluster et une base de données Azure Data Explorer](create-cluster-database-csharp.md).
* Consultez la section [Ingestion des données Azure Data Explorer](ingest-data-overview.md) pour en savoir plus sur les méthodes d’ingestion.
* Pour en savoir plus sur l’application web, voir [Démarrage rapide : Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md).
* [Écrire des requêtes](write-queries.md) avec le langage de requête Kusto.