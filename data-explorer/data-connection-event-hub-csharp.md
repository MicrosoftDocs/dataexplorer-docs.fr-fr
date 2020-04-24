---
title: Créer une connexion de données au hub d’événements pour Azure Data Explorer à l’aide de C#
description: Dans cet article, vous allez apprendre à créer une connexion de données au hub d’événements pour Azure Data Explorer à l’aide de C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 54f7541321b50ae4d3cf679d9224f70cb21a46ab
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493081"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Créer une connexion de données au hub d’événements pour Azure Data Explorer à l’aide de C#

> [!div class="op_single_selector"]
> * [Portail](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Azure Data Explorer offre une ingestion (chargement de données) à partir de hubs d’événements, de hubs IoT et d’objets blob écrits dans des conteneurs d’objets blob. Dans cet article, vous créez une connexion de données au hub d’événements pour Azure Data Explorer à l’aide de C#.

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas encore installé Visual Studio 2019, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.
* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créez [un cluster et une base de données](create-cluster-database-csharp.md).
* Créez [une table et un mappage de colonnes](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).
* Définissez [des stratégies de base de données et de table](database-table-policies-csharp.md) (facultatif).
* Créez [un hub d’événements avec des données pour l’ingestion](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Ajouter une connexion de données au hub d’événements

L’exemple suivant montre comment ajouter programmatiquement une connexion de données au hub d’événements. Consultez [Se connecter au hub d’événements](ingest-data-event-hub.md#connect-to-the-event-hub) pour ajouter une connexion de données au hub d’événements à l’aide du portail Azure.

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Paramètre** | **Valeur suggérée** | **Description du champ**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Votre ID de client. Également appelé ID de répertoire.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID d’abonnement que vous utilisez pour la création de ressources.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| clientSecret | *xxxxxxxxxxxxxx* | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| resourceGroupName | *testrg* | Nom du groupe de ressources qui contient votre cluster.|
| clusterName | *mykustocluster* | Nom de votre cluster.|
| databaseName | *mykustodatabase* | Nom de la base de données cible dans votre cluster.|
| dataConnectionName | *myeventhubconnect* | Nom souhaité de votre connexion de données.|
| tableName | *StormEvents* | Nom de la table cible dans la base de données cible.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Nom de votre mappage de colonnes associé à la table cible.|
| dataFormat | *csv* | Format de données du message.|
| eventHubResourceId | *ID de ressource* | ID de ressource de votre hub d’événements qui contient les données à des fins d’ingestion. |
| consumerGroup | *$Default* | Groupe de consommateurs de votre hub d’événements.|
| location | *USA Centre* | Emplacement de la ressource de connexion de données.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]
