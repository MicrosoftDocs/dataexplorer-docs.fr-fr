---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: b6c50e466df1028f11021e75fe572ea1ecb32225
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942654"
---
## <a name="configure-the-data-source"></a>Configurer la source de données

Effectuez les étapes suivantes pour configurer Azure Data Explorer en tant que source de données pour votre outil de tableau de bord. Nous allons détailler ces étapes dans cette section :

1. Créez un principal de service Azure Active Directory (Azure AD) Le principal du service est utilisé par votre outil de tableau de bord pour accéder au service Azure Data Explorer.

1. Ajoutez le principal de service Azure AD au rôle *observateurs* dans la base de données Azure Data Explorer.

1. Spécifiez les propriétés de connexion de votre outil de tableau de bord selon les informations du principal de service Azure AD, puis testez la connexion.

### <a name="create-a-service-principal"></a>Créer un principal du service

Vous pouvez créer le principal de service dans le [portail Azure](#azure-portal) ou à l’aide de la ligne de commande [Azure CLI](#azure-cli). Quelle que soit la méthode que vous utilisez, après la création, vous obtenez des valeurs pour quatre propriétés de connexion que vous utiliserez lors des étapes ultérieures.

#### <a name="azure-portal"></a>Portail Azure

1. Pour créer le principal de service, suivez les instructions données dans la [documentation du portail Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Dans la section [Affecter l’application à un rôle](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application), affectez le type de rôle **Lecteur** à votre cluster Azure Data Explorer.

    1. Dans la section [Obtenir les valeurs pour la connexion](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), copiez les trois valeurs de propriété décrites dans les étapes : **ID de répertoire** (ID de locataire), **ID d’application** et **Mot de passe**.

1. Dans le portail Azure, sélectionnez **Abonnements**, puis copiez l’ID de l’abonnement dans lequel vous avez créé le principal de service.

    ![ID d’abonnement - portail](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Azure CLI

1. Créer un principal de service. Définissez une étendue appropriée et le type de rôle `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Pour plus d’informations, consultez [Créer un principal de service Azure avec Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

1. La commande retourne un jeu de résultats comme le suivant. Copiez les trois valeurs de propriété : **appID**, **mot de passe** et **locataire**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Obtenez la liste de vos abonnements.

    ```azurecli
    az account list --output table
    ```

    Copiez l’ID d’abonnement approprié.

    ![ID d’abonnement - CLI](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Ajouter le principal de service au rôle observateurs

Maintenant que vous avez un principal de service, ajoutez-le au rôle *observateurs* dans la base de données Azure Data Explorer. Vous pouvez effectuer cette tâche sous **Autorisations** dans le portail Azure ou sous **Requête** en utilisant une commande de gestion.

#### <a name="azure-portal---permissions"></a>Portail Azure - Autorisations

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer.

1. Dans la section **Vue d’ensemble**, sélectionnez la base de données contenant l’exemple de données StormEvents.

    ![Sélectionner une base de données](media/data-explorer-configure-data-source/select-database.png)

1. Sélectionnez **Autorisations**, puis **Ajouter**.

    ![Autorisations de base de données](media/data-explorer-configure-data-source/database-permissions.png)

1. Sous **Ajouter des autorisations de base de données**, sélectionnez le rôle **Observateur**, puis **Sélectionner les principaux**.

    ![Ajouter des autorisations de base de données](media/data-explorer-configure-data-source/add-permission.png)

1. Recherchez le principal de service que vous avez créé. Sélectionnez le principal, puis **Sélectionner**.

    :::image type="content" source="media/data-explorer-configure-data-source/new-principals.png" alt-text="Capture d’écran du volet Nouveaux principaux du portail Azure. Le bouton Sélectionner et deux champs avec des propriétés de principal de service indéchiffrables sont mis en évidence." border="false":::

1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/data-explorer-configure-data-source/save-permission.png" alt-text="Capture d’écran du volet Nouveaux principaux du portail Azure. Le bouton Sélectionner et deux champs avec des propriétés de principal de service indéchiffrables sont mis en évidence." border="false":::

#### <a name="management-command---query"></a>Commande de gestion - Requête

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer, puis sélectionnez **Requête**.

    :::image type="content" source="media/data-explorer-configure-data-source/query.png" alt-text="Capture d’écran du volet Nouveaux principaux du portail Azure. Le bouton Sélectionner et deux champs avec des propriétés de principal de service indéchiffrables sont mis en évidence." border="false":::

1. Exécutez la commande suivante dans la fenêtre de requête. Utiliser l’ID d’application et l’ID de locataire à partir du portail Azure ou de l’interface CLI.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    La commande retourne un jeu de résultats comme le suivant. Dans cet exemple, la première ligne concerne un utilisateur existant dans la base de données et la deuxième le principal de service qui vient d’être ajouté.

    ![Jeu de résultats](media/data-explorer-configure-data-source/result-set.png)
