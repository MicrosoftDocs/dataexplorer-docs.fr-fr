---
title: Ajouter des principaux de base de données pour Azure Data Explorer à l’aide de C#
description: Dans cet article, vous allez apprendre à ajouter des principaux de base de données pour Azure Data Explorer à l’aide de C#.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 97e7b7430410ce73f3577fdf37a2635169ea191b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350024"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Ajouter des principaux de base de données pour Azure Data Explorer à l’aide de C#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Modèle Azure Resource Manager](database-principal-resource-manager.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Dans cet article, vous ajouterez des principaux de base de données pour Azure Data Explorer à l’aide de C#.

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas encore installé Visual Studio 2019, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.
* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Créez un cluster et une base de données](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>Installer le package NuGet C#

* Installez [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installez [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) à des fins d’authentification.

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Ajouter un principal de base de données

L’exemple suivant montre comment ajouter par programmation un principal de base de données.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Paramètre** | **Valeur suggérée** | **Description du champ**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Votre ID de client. Également appelé ID de répertoire.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID d’abonnement que vous utilisez pour la création de ressources.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID client de l’application qui peut accéder aux ressources figurant dans votre locataire.|
| clientSecret | *xxxxxxxxxxxxxx* | Secret client de l’application qui peut accéder aux ressources figurant dans votre locataire. |
| resourceGroupName | *testrg* | Nom du groupe de ressources qui contient votre cluster.|
| clusterName | *mykustocluster* | Nom de votre cluster.|
| databaseName | *mykustodatabase* | Nom de votre base de données.|
| principalAssignmentName | *databasePrincipalAssignment1* | Nom de votre principal de base de données.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID du principal, qui peut être l’e-mail de l’utilisateur, l’ID de l’application ou le nom du groupe de sécurité.|
| rôle | *Administrateur* | Rôle de votre principal de base de données, qui peut être « Admin » (Administrateur), « Ingestor » (Ingesteur), « Monitor » (Superviseur), « User » (Utilisateur), « UnrestrictedViewers » (Utilisateurs en lecture sans restrictions) ou « Viewer » (Utilisateur en lecture seule).|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | ID de locataire du principal.|
| principalType | *Application* | Type du principal, qui peut être « User » (Utilisateur), « App » (Application) ou « Group » (Groupe).|

## <a name="next-steps"></a>Étapes suivantes

* [Ingérer des données à l'aide de la bibliothèque Node d'Azure Data Explorer](node-ingest-data.md)
