---
title: Configurer des clés gérées par le client à l’aide de C#
description: Cet article explique comment configurer des clés gérées par le client pour chiffrer des données Azure Data Explorer en utilisant C#.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: 7f9256f62b03ac63bdae49afbec236b3a05c67e5
ms.sourcegitcommit: 8c0674d2bc3c2e10eace5314c30adc7c9e4b3d44
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571758"
---
# <a name="configure-customer-managed-keys-using-c"></a>Configurer des clés gérées par le client à l’aide de C#

> [!div class="op_single_selector"]
> * [Portail](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
> * [INTERFACE DE LIGNE DE COMMANDE](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurer le chiffrement avec les clés gérées par le client

Cette section vous montre comment configurer le chiffrement avec des clés gérées par le client à l’aide du client C# Azure Data Explorer. 

### <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas encore installé Visual Studio 2019, vous pouvez télécharger et utiliser la version **gratuite** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Veillez à activer **le développement Azure** lors de l’installation de Visual Studio.

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

### <a name="install-c-nuget"></a>Installer le package NuGet C#

* Installez le [package NuGet Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Installez le [package NuGet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pour l’authentification.

### <a name="authentication"></a>Authentification

Pour exécuter les exemples de cet article, [créez une application Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) et un principal de service pouvant accéder aux ressources. Vous pouvez ajouter l’attribution de rôle à l’étendue de l’abonnement et récupérer les `Directory (tenant) ID`, `Application ID` et `Client Secret` requis.

### <a name="configure-cluster"></a>Configurer le cluster

Par défaut, le chiffrement Azure Data Explorer utilise des clés gérées par Microsoft. Configurez votre cluster Azure Data Explorer pour utiliser des clés gérées par le client et spécifiez la clé à lui associer.

1. Mettez à jour votre cluster en utilisant le code suivant :

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
    var clusterName = "mykustocluster";
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654"; // Optional, leave as NULL for the latest version of the key.
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultIdentity = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/identityResourceGroupName/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identityName"; // Use NULL if you want to use system assigned identity.
    var keyVaultProperties = new KeyVaultProperties(keyName, keyVaultUri, keyVersion, keyVaultIdentity);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Exécutez la commande suivante pour vérifier si votre cluster a bien été mis à jour :

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Si le résultat contient `ProvisioningState` avec la valeur `Succeeded`, alors votre cluster a correctement été mis à jour.

## <a name="update-the-key-version"></a>Mettre à jour la version de la clé

Lors de la création de la nouvelle version d’une clé, vous devez mettre à jour le cluster afin qu’il utilise cette nouvelle version. Tout d’abord, appelez la commande `Get-AzKeyVaultKey` pour obtenir la dernière version de la clé. Ensuite, mettez à jour les propriétés du coffre de clés du cluster pour utiliser la nouvelle version de la clé, comme indiqué dans [Configurer un cluster](#configure-cluster).

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les clusters Azure Data Explorer dans Azure](security.md)
* [Configurer des identités managées pour votre cluster Azure Data Explorer](managed-identities.md)
* [Sécuriser votre cluster en utilisant le chiffrement de disque dans Azure Data Explorer – Portail Azure](cluster-disk-encryption.md) en activant le chiffrement au repos.
* [Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
