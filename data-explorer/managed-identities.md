---
title: Comment configurer des identités managées pour le cluster Azure Data Explorer
description: Découvrez comment configurer des identités managées pour le cluster Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 121d9d3839b86b302fc15e9299797ac3ae3c6835
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874730"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Configurer des identités managées pour votre cluster Azure Data Explorer

Une [identité managée issue d’Azure Active Directory](/azure/active-directory/managed-identities-azure-resources/overview) permet à votre cluster d’accéder facilement aux autres ressources protégées par AAD telles qu’Azure Key Vault. Managée par la plateforme Azure, l’identité ne nécessite pas que vous approvisionniez ou permutiez de secrets. Cet article explique comment créer une identité managée pour des clusters Azure Data Explorer. La configuration des identités managées est actuellement prise en charge uniquement pour [activer les clés gérées par le client pour votre cluster](security.md#customer-managed-keys-with-azure-key-vault).

> [!Note]
> Les identités managées pour Azure Data Explorer ne se comportent pas comme prévu si votre cluster Azure Data Explorer est migré entre des abonnements ou des locataires. L’application devra obtenir une nouvelle identité, ce qui est possible en [désactivant](#disable-a-system-assigned-identity) et en [réactivant](#add-a-system-assigned-identity) la fonctionnalité. Les stratégies d’accès des ressources en aval devront également être mises à jour pour utiliser la nouvelle identité.

## <a name="add-a-system-assigned-identity"></a>Ajouter une identité affectée par le système
                                                                                                    
Attribuez une identité affectée par le système liée à votre cluster, qui sera supprimée en cas de suppression du cluster. Un cluster ne peut avoir qu’une seule identité attribuée par le système. Créer un cluster avec une identité attribuée par le système requiert la définition d’une propriété supplémentaire sur le cluster. L’identité affectée par le système peut être ajoutée en C#, à l’aide de modèles ARM ou sur le Portail Azure, comme indiqué ci-dessous.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Ajout d’une identité affectée par le système à l’aide du Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

#### <a name="new-azure-data-explorer-cluster"></a>Nouveau cluster Azure Data Explorer

1. [Créez un cluster Azure Data Explorer](create-cluster-database-portal.md#create-a-cluster). 
1. Dans l’onglet **Sécurité** > **Identité affectée par le système**, sélectionnez **Activée**. Pour supprimer l’identité affectée par le système, sélectionnez **Désactivée**.
2. Sélectionnez **Suivant : Balises >** ou **Examiner + Créer** pour créer le cluster.

    ![Ajout d’une identité affectée par le système au nouveau cluster](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Cluster Azure Data Explorer existant

1. Ouvrez un cluster Azure Data Explorer existant.
1. Sélectionnez **Paramètres** > **Identité** dans le volet gauche du portail.
1. Dans le volet **Identité** > onglet **Affectée par le système** :
   1. Déplacez le curseur **État** sur **Activé**.
   1. Sélectionnez **Enregistrer**.
   1. Dans la fenêtre indépendante qui s’affiche, sélectionnez **Oui**.

    ![Ajout d’une identité affectée au système](media/managed-identities/turn-system-assigned-identity-on.png)

1. Après quelques minutes, l’écran indique : 
  * **ID objet** : utilisé pour les clés gérées par le client. 
  * **Attributions de rôles** : cliquez sur le lien pour affecter les rôles appropriés.

    ![Identité affectée par le système activée](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Ajouter une identité affectée par le système à l’aide du langage C#

#### <a name="prerequisites"></a>Prérequis

Pour configurer une identité managée à l’aide du client C# Azure Data Explorer :

* Installez le [package NuGet Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installez le [package NuGet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) pour l’authentification.
* [Créez une application Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal) et un principal de service ayant accès aux ressources. Ajoutez l’attribution de rôle à l’étendue de l’abonnement et récupérez les `Directory (tenant) ID`, `Application ID` et `Client Secret` requis.

#### <a name="create-or-update-your-cluster"></a>Créer ou mettre à jour votre cluster

1. Créez ou mettez à jour votre cluster à l’aide de la propriété `Identity` :

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
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. Exécutez la commande suivante pour vérifier si votre cluster a été correctement créé ou mis à jour avec une identité :

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Si le résultat contient `ProvisioningState` avec la valeur `Succeeded`, le cluster a été créé ou mis à jour et doit avoir les propriétés suivantes :

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId` et `TenantId` sont remplacés par des GUID. La propriété `TenantId` identifie le locataire AAD auquel appartient l’identité. Le `PrincipalId` est un identificateur unique pour la nouvelle identité du cluster. Dans AAD, le principal de service porte le même nom que celui que vous avez donné à votre instance App Service ou Azure Functions.

# <a name="arm-template"></a>[Modèle ARM](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Ajouter une identité affectée par le système à l’aide d’un modèle Azure Resource Manager

Vous pouvez utiliser un modèle Azure Resource Manager pour automatiser le déploiement de vos ressources Azure. Pour en savoir plus sur le déploiement sur Azure Data Explorer, consultez [Créer un cluster Azure Data Explorer et une base de données à l’aide d’un modèle Azure Resource Manager](create-cluster-database-resource-manager.md).

L’ajout du type attribué par le système indique à Azure de créer et de manager l’identité de votre cluster. Vous pouvez créer n’importe quelle ressource de type `Microsoft.Kusto/clusters` avec une identité en incluant la propriété suivante dans la définition de la ressource : 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Par exemple :

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

Quand le cluster est créé, il a les propriétés supplémentaires suivantes :

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>` et `<PRINCIPALID>` sont remplacés par des GUID. La propriété `TenantId` identifie le locataire AAD auquel appartient l’identité. Le `PrincipalId` est un identificateur unique pour la nouvelle identité du cluster. Dans AAD, le principal de service porte le même nom que celui que vous avez donné à votre instance App Service ou Azure Functions.

---

## <a name="disable-a-system-assigned-identity"></a>Désactiver une identité attribuée par le système

Si vous supprimez une identité attribuée par le système, vous la supprimez également d’AAD. Les identités attribuées par le système sont aussi automatiquement supprimées d’AAD lorsque la ressource de cluster est supprimée. Une identité affectée par le système peut être supprimée en désactivant la fonctionnalité,  en C#, à l’aide de modèles ARM ou sur le Portail Azure, comme indiqué ci-dessous.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

### <a name="disable-a-system-assigned-identity-using-the-azure-portal"></a>Désactiver une identité attribuée par le système à l’aide du Portail Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Sélectionnez **Paramètres** > **Identité** dans le volet gauche du portail.
1. Dans le volet **Identité** > onglet **Affectée par le système** :
    1. Déplacez le curseur **État** sur **Désactivé**.
    1. Sélectionnez **Enregistrer**.
    1. Dans la fenêtre indépendante, sélectionnez **Oui** pour désactiver l’identité affectée par le système. Le volet **Identité** revient à la situation antérieure à l’ajout de l’identité affectée par le système.

    ![Identité affectée par le système désactivée](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C#](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Suppression d’une identité affectée par le système en C#

Exécutez le code suivant pour supprimer l’identité affectée par le système :

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[Modèle ARM](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Suppression d’une identité affectée par le système à l’aide d’un modèle Azure Resource Manager

Exécutez le code suivant pour supprimer l’identité affectée par le système :

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser les clusters Azure Data Explorer dans Azure](security.md)
* [Sécuriser votre cluster en utilisant le chiffrement de disque dans Azure Data Explorer – Portail Azure](cluster-disk-encryption.md) en activant le chiffrement au repos.
 * [Configurer des clés gérées par le client à l’aide de C#](customer-managed-keys-csharp.md)
 * [Configurer des clés gérées par le client à l’aide du modèle Azure Resource Manager](customer-managed-keys-resource-manager.md)
