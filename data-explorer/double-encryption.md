---
title: Activer le chiffrement d’infrastructure (double chiffrement) lors de la création de cluster dans Azure Data Explorer
description: Cet article décrit comment activer le chiffrement d’infrastructure (double chiffrement) lors de la création de cluster dans Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: toleibov
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/11/2020
ms.openlocfilehash: 4949190befdc8adcec9f8115305a2a403994395f
ms.sourcegitcommit: 88291fd9cebc26e5210463cb95be5540bf84eef8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92437395"
---
# <a name="enable-infrastructure-encryption-double-encryption-during-cluster-creation-in-azure-data-explorer"></a>Activer le chiffrement d’infrastructure (double chiffrement) lors de la création de cluster dans Azure Data Explorer
  
Lorsque vous créez un cluster, son stockage est [automatiquement chiffré au niveau du service](/azure/storage/common/storage-service-encryption). Si vous avez besoin d’un niveau plus élevé de garantie que vos données sont sécurisées, vous pouvez également activer le [Chiffrement au niveau de l’infrastructure de Stockage Azure](/azure/storage/common/infrastructure-encryption-enable), également appelé double chiffrement. Lorsque le chiffrement d’infrastructure est activé, les données d’un compte de stockage sont chiffrées deux fois, une fois au niveau du service et une fois au niveau de l’infrastructure, avec deux algorithmes de chiffrement différents et deux clés différentes. Le double chiffrement des données Stockage Azure permet d’éviter un scénario impliquant une possible compromission d’un algorithme ou d’une clé de chiffrement. Dans un tel scénario, la couche de chiffrement supplémentaire continue de protéger vos données.

> [!IMPORTANT]
> * L’activation du double chiffrement est possible uniquement lors de la création de cluster.
> * Une fois que le chiffrement d’infrastructure est activé sur votre cluster, vous **ne pouvez pas** le désactiver.

# <a name="azure-portal"></a>[Portail Azure](#tab/portal)

1. [Créez un cluster Azure Data Explorer](create-cluster-database-portal.md#create-a-cluster). 
1. Sous l’onglet **Sécurité** > **Activer le chiffrement double** , sélectionnez **Activé** . Pour supprimer le chiffrement double, sélectionnez **Désactivé** .
1. Sélectionnez **Suivant : Réseau >** ou **Vérifier + créer** pour créer le cluster.

    :::image type="content" source="media/double-encryption/double-encryption-portal.png" alt-text="chiffrement double et nouveau cluster":::


# <a name="c"></a>[C#](#tab/c-sharp)

Vous pouvez activer le chiffrement d’infrastructure lors de la création de cluster en utilisant C#.

## <a name="prerequisites"></a>Prérequis

Configurez une identité managée à l’aide du client C# Azure Data Explorer :

* Installez le [package NuGet Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installez le [package NuGet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pour l’authentification.
* [Créez une application Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) et un principal de service ayant accès aux ressources. Ajoutez l’attribution de rôle à l’étendue de l’abonnement et récupérez les `Directory (tenant) ID`, `Application ID` et `Client Secret` requis.

## <a name="create-your-cluster"></a>Création de votre cluster

1. Créez votre cluster à l’aide de la propriété `enableDoubleEncryption` :

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
    var location = "East US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var enableDoubleEncryption = true;
    var cluster = new Cluster(location, sku, enableDoubleEncryption: enableDoubleEncryption);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
1. Exécutez la commande suivante pour vérifier si votre cluster a bien été créé :

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Si le résultat contient `ProvisioningState` avec la valeur `Succeeded`, le cluster a correctement été créé.

# <a name="arm-template"></a>[Modèle ARM](#tab/arm)

Vous pouvez activer le chiffrement d’infrastructure lors de la création de cluster en utilisant Azure Resource Manager.

Vous pouvez utiliser un modèle Azure Resource Manager pour automatiser le déploiement de vos ressources Azure. Pour en savoir plus sur le déploiement sur Azure Data Explorer, consultez [Créer un cluster Azure Data Explorer et une base de données à l’aide d’un modèle Azure Resource Manager](create-cluster-database-resource-manager.md).

## <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Ajouter une identité affectée par le système à l’aide d’un modèle Azure Resource Manager

1. Ajoutez le type « EnableDoubleEncryption » pour indiquer à Azure d’activer le chiffrement d’infrastructure (chiffrement double) pour votre cluster.
    
    ```json
    {
        "apiVersion": "2020-06-14",
        "type": "Microsoft.Kusto/clusters",
        "name": "[variables('clusterName')]",
        "location": "[resourceGroup().location]",
        "properties": {
            "trustedExternalTenants": [],
            "virtualNetworkConfiguration": null,
            "optimizedAutoscale": null,
            "enableDiskEncryption": false,
            "enableStreamingIngest": false,
            "enableDoubleEncryption": true,
        }
    }
    ```

1. Quand le cluster est créé, il a les propriétés supplémentaires suivantes :

    ```json
    "identity": {
        "type": "SystemAssigned",
        "tenantId": "<TENANTID>",
        "principalId": "<PRINCIPALID>"
    }
    ```
---

## <a name="next-steps"></a>Étapes suivantes

[Vérifier l’intégrité des clusters](check-cluster-health.md)
