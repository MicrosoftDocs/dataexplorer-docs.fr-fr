---
title: Créer une application Azure AD dans Azure Data Explorer
description: Découvrez comment créer une application Azure AD dans Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 7bbb8064cd71e61b6fdfb1205e783099ec21c687
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494257"
---
# <a name="create-an-azure-active-directory-application-registration-in-azure-data-explorer"></a>Créer une inscription d’application Azure Active Directory dans Azure Data Explorer

L’authentification d’application Azure Active Directory (Azure AD) est utilisée pour des applications, telles qu’un service autonome ou un flux planifié, qui doivent pouvoir accéder à Azure Data Explorer sans la présence d’un utilisateur. Si vous vous connectez à une base de données Azure Data Explorer à l’aide d’une application telle qu’une application web, vous devez procéder à une authentification du principal du service. Cet article explique comment créer et inscrire un principal de service Azure AD et comment l’autoriser à accéder à une base de données Azure Data Explorer.

## <a name="create-azure-ad-application-registration"></a>Créer une inscription d’application Azure AD

L’authentification d’une application Azure AD nécessite de créer une application et de l’inscrire auprès d’Azure AD. Un principal du service est automatiquement créé lors de la création de l’inscription d’application dans un locataire Azure AD. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) et ouvrez le panneau `Azure Active Directory`.

    ![Sélectionner Azure Active Directory dans le menu du portail](media/provision-azure-ad-app/create-app-reg-select-azure-active-directory.png)

1. Sélectionnez le panneau **Inscriptions d’applications**, puis choisissez **Nouvelle inscription**.

    ![Démarrer une inscription d’application](media/provision-azure-ad-app/create-app-reg-new-registration.png)

1. Renseignez les informations suivantes : 

    * **Nom** 
    * **Types de comptes pris en charge**
    * **URI de redirection** > **Web**
        > [!IMPORTANT] 
        > Le type d’application doit être **Web**. L’URI est facultatif et laissé vide dans ce cas.
    * Sélectionnez **Inscrire**.

    ![Inscrire une nouvelle inscription d’application](media/provision-azure-ad-app/create-app-reg-register-app.png)

1. Sélectionnez le panneau **Vue d’ensemble** et copiez l’**ID d’application**.

    > [!NOTE]
    > Vous aurez besoin de l’ID d’application pour autoriser le principal du service à accéder à la base de données.

    ![Copier l’ID d’inscription d’application](media/provision-azure-ad-app/create-app-reg-copy-applicationid.png)

1. Dans le panneau **Certificats et secrets**, sélectionnez **Nouveau secret client**.

    ![Démarrer la création d’une clé secrète client](media/provision-azure-ad-app/create-app-reg-new-client-secret.png)

    > [!TIP]
    > Cet article décrit l’utilisation d’une clé secrète client pour les informations d’identification de l’application.  Vous pouvez également utiliser un certificat x509 pour authentifier votre application. Sélectionnez **Télécharger un certificat**, pus suivez les instructions pour charger la partie publique du certificat.

1. Entrez une description, une expiration, puis sélectionnez **Ajouter**.

    ![Entrer les paramètres de la clé secrète client](media/provision-azure-ad-app/create-app-reg-enter-client-secret-details.png)

1. Copiez la valeur de la clé.

    > [!NOTE]
    > Lorsque vous fermez cette page, la valeur de la clé n’est pas accessible.  Vous aurez besoin de la clé pour configurer les informations d’identification du client pour l’accès à la base de données.

    ![Copier la valeur de clé secrète client](media/provision-azure-ad-app/create-app-reg-copy-client-secret.png)

Votre application est créée. Si vous avez seulement besoin d’accéder à une ressource Azure Data Explorer autorisée, comme dans l’exemple de programmation ci-dessous, ignorez la section suivante. Pour la prise en charge des autorisations déléguées, consultez [Configurer des autorisations déléguées pour l’inscription d’application](#configure-delegated-permissions-for-the-application-registration).

## <a name="configure-delegated-permissions-for-the-application-registration"></a>Configurer des autorisations déléguées pour l’inscription d’application

Si votre application doit accéder à Azure Data Explorer à l’aide des informations d’identification de l’utilisateur appelant, configurez les autorisations déléguées pour l’inscription de votre application. Par exemple, si vous créez une API web pour accéder à Azure Data Explorer et vous souhaitez vous authentifier à l’aide des informations d’identification de l’utilisateur *appelant* votre API.  

1. Dans le panneau **Autorisations de l’API**, sélectionnez **Ajouter une autorisation**.
1. Sélectionnez **API utilisées par mon organisation**. Recherchez et sélectionnez **Azure Data Explorer**.

    ![Ajouter une autorisation d’API Azure Data Explorer](media/provision-azure-ad-app/configure-delegated-add-api-permission.png)

1. Dans **Autorisations déléguées**, activez la case à cocher **user_impersonation** et sélectionnez **Ajouter des autorisations**.

    ![Sélectionner des autorisations déléguées avec emprunt d’identité de l’utilisateur](media/provision-azure-ad-app/configure-delegated-click-add-permissions.png)     

## <a name="grant-the-service-principal-access-to-an-azure-data-explorer-database"></a>Accorder au principal du service l’accès à une base de données Azure Data Explorer

À présent que l’inscription de votre application de principal du service est créée, vous devez accorder au principal du service correspondant l’accès à votre base de données Azure Data Explorer. 

1. Dans l’[interface utilisateur web](https://dataexplorer.azure.com/), connectez-vous à votre base de données, puis ouvrez un onglet de requête.

1. Exécutez la commande suivante :

    ```kusto
    .add database <DatabaseName> viewers ('<ApplicationId>') '<Notes>'
    ```

    Par exemple :
    
    ```kusto
    .add database Logs viewers ('aadapp=f778b387-ba15-437f-a69e-ed9c9225278b') 'Azure Data Explorer App Registration'
    ```

    Le dernier paramètre est une chaîne qui s’affiche sous la forme de notes lorsque vous interrogez les rôles associés à une base de données.
    
    > [!NOTE]
    > Après la création de l’inscription d’application, un délai de plusieurs minutes peut s’écouler avant qu’Azure Data Explorer puisse la référencer. Si, lors de l’exécution de la commande, vous recevez une erreur indiquant que l’application est introuvable, patientez et réessayez.

Pour plus d’informations, consultez [gestion des rôles de sécurité](kusto/management/security-roles.md) et [autorisations d’ingestion](kusto/api/netfx/kusto-ingest-client-permissions.md).  

## <a name="using-application-credentials-to-access-a-database"></a>Utilisation des informations d’identification de l’application pour accéder à une base de données

Utilisez les informations d’identification de l’application pour accéder à votre base de données par programme en utilisant la [bibliothèque cliente Azure Data Explorer](kusto/api/netfx/about-kusto-data.md).

```C#
. . .
string applicationClientId = "<myClientID>";
string applicationKey = "<myApplicationKey>";
. . .
var kcsb = new KustoConnectionStringBuilder($"https://{clusterName}.kusto.windows.net/{databaseName}")
    .WithAadApplicationKeyAuthentication(
        applicationClientId,
        applicationKey,
        authority);
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery($"{query}");
```

   > [!NOTE]
   > Spécifiez l’ID d’application et la clé de l’inscription d’application (principal du service) créée précédemment.

Pour plus d’informations, consultez [Comment s’authentifier avec Azure AD pour accéder à Azure Data Explorer](kusto/management/access-control/how-to-authenticate-with-aad.md) et [Utiliser Azure Key Vault avec une application web .NET Core](/azure/key-vault/tutorial-net-create-vault-azure-web-app#create-a-net-core-web-app).

## <a name="troubleshooting"></a>Dépannage

### <a name="invalid-resource-error"></a>Erreur de ressource non valide

Si votre application est utilisée pour authentifier des utilisateurs ou des applications pour l’accès à Azure Data Explorer, vous devez configurer des autorisations déléguées pour l’application de service Azure Data Explorer. Déclarez que votre application peut authentifier des utilisateurs ou des applications pour l’accès à Azure Data Explorer. Si vous ne le faites pas, une erreur semblable à la suivante se produit en cas de tentative d’authentification :

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Vous devez suivre les instructions relatives à la [configuration d’autorisations déléguées pour une application de service Azure Data Explorer](#configure-delegated-permissions-for-the-application-registration).

### <a name="enable-user-consent-error"></a>Activer l’erreur de consentement de l’utilisateur

Votre administrateur client Azure AD peut promulguer une stratégie qui empêche les utilisateurs locataires de donner leur consentement aux applications. Dans cette situation, une erreur similaire à la suivante se produit quand un utilisateur tente de se connecter à votre application :

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Vous devez contacter votre administrateur Azure AD pour donner votre consentement pour tous les utilisateurs du locataire, ou activer le consentement de l’utilisateur pour votre application spécifique.

## <a name="next-steps"></a>Étapes suivantes

* Consultez [Chaînes de connexion Kusto](kusto/api/connection-strings/kusto.md) pour obtenir la liste des chaînes de connexion prises en charge.
