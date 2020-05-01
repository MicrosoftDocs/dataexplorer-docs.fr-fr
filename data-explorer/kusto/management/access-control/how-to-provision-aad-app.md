---
title: Comment créer une application AAD-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit comment créer une application AAD dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 0816d55cda6d29820084514b0bfec27d726693f9
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617967"
---
# <a name="howto-creating-an-aad-application"></a>Procédure : création d’une application AAD

L’authentification des utilisateurs AAD est très facile (car les utilisateurs sont définis dans AAD par l’administrateur client, par exemple MSIT), l’authentification de l’application AAD est un peu plus complexe. Cela est dû au fait que vous devez créer et inscrire l’application avec AAD. Ce processus est décrit ci-dessous dans certains détails.

L’authentification d’application AAD est utile pour les applications qui doivent accéder à Kusto sans qu’un utilisateur soit connecté ou présent (par exemple, un service sans assistance ou un workflow planifié).

Les applications clientes et les applications de couche intermédiaire qui ont un contexte utilisateur interactif doivent éviter ce modèle. Cela est dû au fait que l’autorisation est effectuée en fonction de l’identité de l’application AAD au lieu de l’identité de l’utilisateur, de sorte que l’application appelante doit implémenter sa propre logique d’autorisation pour éviter toute utilisation abusive.

## <a name="application-authentication-use-cases"></a>Cas d’utilisation de l’authentification d’application

Il existe deux principaux scénarios qui utilisent l’authentification de l’application :
* Applications qui contactent le service Kusto directement et en leur nom propre
* Applications qui authentifieront leurs utilisateurs sur Kusto (authentification déléguée)

## <a name="1-provisioning-a-new-application"></a>1. Configuration d’une nouvelle application

#### <a name="register-the-new-application"></a>Inscrire la nouvelle application

1. Connectez-vous à [portail Azure](https://portal.azure.com) et ouvrez `Azure Active Directory` le panneau.

    :::image type="content" source="images/aad-create-app-step-0.png" alt-text="Étape de création d’une application AAD 0":::

1. Choisissez `App registrations` et lorsque le panneau est chargé, `New application registration`sélectionnez.

    :::image type="content" source="images/aad-create-app-step-1.png" alt-text="Étape de création d’une application AAD 1":::

1. Renseignez les détails de l’application :
    * Nom
    * Type : défini sur`Web app/API`
    * URL de connexion : URL utilisée par les utilisateurs pour accéder à l’application. AAD ne valide pas l’URL,<br>
        Toutefois, vous oblige à fournir une valeur. Par conséquent, si l’application n’a pas d’URL accessible aux utilisateurs,<br>
        Entrez une URL qui appartient à l’application (par exemple, https://<APP-CNAMe> ou https://<CLOUD-SERVICE-NAME>. cloudapp.net).<br>
        Vous pouvez même fournir une valeur et continuer si l’application n’a pas encore été écrite.

    
    :::image type="content" source="images/aad-create-app-step-2.png" alt-text="Étape de création d’une application AAD 2":::

1. Une fois que votre application est prête, `Settings` ouvrez son panneau.

    :::image type="content" source="images/aad-create-app-step-3.png" alt-text="Créer l’étape 3 AAD":::

1. Dans le `Keys` panneau, configurez l’authentification pour votre nouvelle application :
    * Si vous souhaitez utiliser une clé partagée, sélectionnez durée de la clé dans le menu déroulant et copiez la clé lorsqu’elle est générée.
        Vous ne pourrez pas restaurer cette clé.
    * Vous pouvez également utiliser un certificat x509 pour authentifier votre application.
        Pour ce faire, sélectionnez `Upload Public Key` et suivez les instructions pour télécharger la partie publique du certificat.
    * N’oubliez pas de `Save` sélectionner lorsque vous avez terminé.

    :::image type="content" source="images/aad-create-app-step-4.png" alt-text="Étape de création d’une application AAD 4":::

Votre application est configurée. Si vous avez seulement besoin d’accéder à Kusto avec l’application que vous venez de créer, vous avez terminé.

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>Configurer des autorisations déléguées pour l’application de service Kusto

Si vous avez besoin que l’application soit en mesure d’effectuer l’authentification de l’utilisateur ou de l’application pour l’accès Kusto, vous devez configurer l’approbation entre votre application et Kusto :

1. Dans le `Required permissions` panneau, sélectionnez `Add`.

    :::image type="content" source="images/aad-create-app-step-5.png" alt-text="Étape 5 de création d’une application AAD":::
   
1. Choisissez `Select an API`, entrez `KustoService` dans la zone de filtre et `KustoService (Kusto)`sélectionnez.
1. Si vos clusters Kusto nécessitent MFA, entrez `KustoMFA` dans la zone de filtre et `KustoServiceMFA (KustoMFA)`sélectionnez.

    :::image type="content" source="images/aad-create-app-step-6.png" alt-text="Étape de création d’une application AAD 6":::

1. Après avoir confirmé votre sélection, choisissez autorisations déléguées pour `Access Kusto`.

   :::image type="content" source="images/aad-create-app-step-7.png" alt-text="Étape de création d’une application AAD 7":::

1. Sélectionnez `Done` cette option pour terminer le processus.


### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. définir des autorisations sur l’application sur le cluster Kusto

> Avant d’utiliser votre application nouvellement approvisionnée, vérifiez qu’elle est résolue à partir de API Graph.<br>
    La propagation des modifications AAD peut prendre jusqu’à plusieurs heures.

1. Accédez à votre administrateur de base de données pour accorder des autorisations à la nouvelle application.
Pour plus d’informations sur la définition des autorisations, consultez la section [gestion des principaux de base de données](../security-roles.md) .<br>
Pour configurer les autorisations d’ingestion, consultez [autorisations](../../api/netfx/kusto-ingest-client-permissions.md)d’ingestion.
1. Ajoutez une connexion à ce service dans votre explorateur Kusto, si vous ne l’avez pas encore.

### <a name="3-application-can-now-access-kusto"></a>3. l’application peut maintenant accéder à Kusto

Lorsque vous utilisez votre application nouvellement approvisionnée pour accéder aux clusters Kusto à l’aide de bibliothèques clientes Kusto, spécifiez la chaîne de connexion suivante (si vous choisissez de vous authentifier avec une clé partagée) :

`https://`*ClusterDnsName*`/;Federated Security=True;Application Client Id=`*ApplicationClientId*ApplicationClientId`;Application Key=`*ApplicationKey*


### <a name="appendix-a-aad-errors"></a>Annexe A : erreurs AAD

#### <a name="aad-error-aadsts650057"></a>AADSTS650057 d’erreurs AAD

Si votre application est utilisée pour authentifier des utilisateurs ou des applications pour l’accès Kusto, vous devez configurer des autorisations déléguées pour l’application de service Kusto, c.-à-d. déclarer que votre application peut authentifier des utilisateurs ou des applications pour l’accès Kusto.
Si vous ne le faites pas, une erreur semblable à la suivante se produit en cas de tentative d’authentification :

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Vous devrez suivre les instructions relatives à la [Configuration des autorisations déléguées pour l’application de service Kusto](#set-up-delegated-permissions-for-kusto-service-application).

#### <a name="enable-user-consent-if-needed"></a>Activer le consentement de l’utilisateur, le cas échéant

Votre administrateur de locataire AAD peut mettre en œuvre une stratégie qui empêche les utilisateurs locataires de donner leur consentement aux applications. Cela génère une erreur similaire à la suivante, lorsqu’un utilisateur tente de se connecter à votre application :

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Vous devez demander à votre administrateur AAD d’accorder le consentement pour tous les utilisateurs du locataire, ou activer le consentement de l’utilisateur pour votre application spécifique.

### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>Annexe B : Rubriques avancées et résolution des problèmes

* Consultez la documentation sur les [chaînes de connexion Kusto](../../api/connection-strings/kusto.md) pour obtenir une référence complète des chaînes de connexion prises en charge
