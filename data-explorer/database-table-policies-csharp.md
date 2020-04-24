---
title: Créer des stratégies à l’aide du Kit de développement logiciel (SDK) C# d’Azure Data Explorer
description: Dans cet article, vous apprendrez à créer des stratégies en utilisant le langage C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: ee9d740a3bd9748611b4e822f5204eee2633b1bf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492809"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Créer des stratégies de base de données et de table pour Azure Data Explorer en utilisant le langage C#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Dans cet article, vous créerez des stratégies de base de données et de table pour Azure Data Explorer en utilisant le langage C#.

## <a name="prerequisites"></a>Prérequis

* Visual Studio 2019. Si vous n’avez pas Visual Studio 2019, vous pouvez télécharger et utiliser la version *gratuite* de [Visual Studio Community 2019](https://www.visualstudio.com/downloads/). Veillez à sélectionner **Développement Azure** lors de la configuration de Visual Studio.
* Un abonnement Azure. Si nécessaire, vous pouvez créer un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Un cluster et une base de données de test](create-cluster-database-csharp.md).
* [Une table de test](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>Installer le package NuGet C#

* Installez le [package NuGet Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installez le [package NuGet Microsoft.Azure.Kusto.Data.NETStandard](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Facultatif, pour modifier les stratégies de la table.)
* Installez le [package NuGet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pour l’authentification.

## <a name="authentication"></a>Authentification
Une application Azure Active Directory (Azure AD) et un principal de service ayant accès aux ressources sont nécessaires pour exécuter les exemples de cet article. Vous pouvez utiliser la même application Azure AD pour l’authentification à partir [d’un cluster de test et d’une base de données](create-cluster-database-csharp.md#authentication). Si vous souhaitez utiliser une autre application Azure AD, consultez [Créer une application Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) pour créer une application Azure AD gratuite et ajouter une attribution de rôle au niveau de l’étendue de l’abonnement. Cet article montre également comment obtenir `Directory (tenant) ID`, `Application ID` et `Client secret`. Vous devrez peut-être ajouter la nouvelle application Azure AD en tant que principal dans la base de données. Pour plus d’informations, consultez [Gérer les autorisations de base de données d’Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).

## <a name="alter-database-retention-policy"></a>Stratégie de conservation de modification de base de données
Définit une stratégie de rétention avec une période de suppression réversible de 10 jours.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Stratégie de cache de modification de base de données
Définit une stratégie de cache pour la base de données. Les cinq derniers jours de données seront sur le disque SSD de cluster.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Stratégie de cache de modification de table
Définit une stratégie de cache pour la table. Les cinq derniers jours de données seront sur le disque SSD de cluster.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>Ajouter un nouveau principal pour la base de données
Ajoute une nouvelle application Azure AD en tant que principal d’administration pour la base de données.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les stratégies de base de données et de table](kusto/management/policies.md)
