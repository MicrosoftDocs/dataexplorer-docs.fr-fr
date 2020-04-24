---
title: HowTo - Création d’une application AAD - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit HowTo - Création d’une application AAD dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 91d646d3bd0922f9daf9e8caec6fa6be5e32e2b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522897"
---
# <a name="howto-creating-an-aad-application"></a>HowTo: Création d’une application AAD

Bien que l’authentification de l’utilisateur AAD soit très facile (parce que les utilisateurs sont définis dans AAD par l’administrateur locataire, comme MSIT), l’authentification de l’application AAD est un peu plus complexe. C’est parce qu’il vous oblige à créer et enregistrer l’application avec AAD. Ce processus est décrit ci-dessous dans certains détails.

L’authentification de l’application AAD est utile pour les applications qui doivent accéder à Kusto sans qu’un utilisateur ne soit connecté ou présent (par exemple, un service sans surveillance ou un flux régulier).

Les applications client et les applications de niveau intermédiaire qui ont un contexte utilisateur interactif devraient éviter ce modèle. C’est parce que l’autorisation est effectuée en fonction de l’identité de l’application AAD au lieu de l’identité de l’utilisateur, de sorte que l’application d’appel devra mettre en œuvre sa propre logique d’autorisation pour prévenir l’utilisation abusive.

## <a name="application-authentication-use-cases"></a>Cas d’utilisation d’authentification d’applications

Il y a deux scénarios principaux qui font usage de l’authentification d’application :
* Applications qui communiquent directement avec le service Kusto et en leur propre nom
* Applications qui authentifieront leurs utilisateurs à Kusto (authentification déléguée)

## <a name="1-provisioning-a-new-application"></a>1. Fourniture d’une nouvelle demande

#### <a name="register-the-new-application"></a>Enregistrer la nouvelle demande

1. Connectez-vous au portail `Azure Active Directory` [Azure](https://portal.azure.com) et ouvrez la lame.

    ![texte de remplacement](./images/Aad-create-app-step-0.png "Aad-create-app-step-0")

1. Choisissez `App registrations` et quand la lame se charge, sélectionnez `New application registration`.

    ![texte de remplacement](./images/Aad-create-app-step-1.png "Aad-create-app-step-1")

1. Remplissez les détails de l’application :
    * Nom
    * Type: réglé à`Web app/API`
    * URL de connexion : l’URL utilisée par les utilisateurs pour accéder à l’application. AAD ne valide pas l’URL,<br>
        mais exige que vous fournissiez une valeur. Donc, si l’application n’a pas d’URL accessible par les utilisateurs,<br>
        entrez une URL qui appartient à l’application (comme https://<APP-CNAME> ou https://<CLOUD-SERVICE-NAME>.cloudapp.net).<br>
        Vous pouvez même fournir une valeur et continuer si l’application n’a pas encore été écrite.

    ![texte de remplacement](./images/Aad-create-app-step-2.png "Aad-create-app-step-2")

1. Une fois votre application `Settings` prête, ouvrez sa lame.

    ![texte de remplacement](./images/Aad-create-app-step-3.png "Aad-create-app-step-3")

1. Sur `Keys` la lame, configurez l’authentification de votre nouvelle application :
    * Si vous souhaitez utiliser une clé partagée, sélectionnez la durée de la clé dans le menu déroulant et copiez la clé lorsqu’elle est générée.
        Vous ne serez pas en mesure de restaurer cette clé.
    * Vous pouvez également utiliser un certificat X509 pour authentifier votre demande.
        Pour ce faire, sélectionnez `Upload Public Key` et suivez les instructions pour télécharger la partie publique du certificat.
    * N’oubliez pas `Save` de choisir quand vous avez terminé.

    ![texte de remplacement](./images/Aad-create-app-step-4.png "Aad-create-app-step-4")

Votre application est configuré. Si tout ce dont vous avez besoin est l’accès à Kusto avec l’application nouvellement créée, vous avez terminé.

#### <a name="set-up-delegated-permissions-for-kusto-service-application"></a>Mettre en place des autorisations déléguées pour la demande de service Kusto

Si vous avez besoin de l’application pour être en mesure d’effectuer l’authentification de l’utilisateur ou de l’application pour l’accès Kusto, vous devez configurer la confiance entre votre application et Kusto:

1. Sur `Required permissions` la lame, sélectionnez `Add`.

    ![texte de remplacement](./images/Aad-create-app-step-5.png "Aad-create-app-step-5")

1. Choisissez `Select an API`, `KustoService` entrez dans `KustoService (Kusto)`la boîte de filtre et sélectionnez .
1. Si vos clusters Kusto nécessitent `KustoMFA` un AMF, `KustoServiceMFA (KustoMFA)`entrez dans la boîte de filtre et sélectionnez .

    ![texte de remplacement](./images/Aad-create-app-step-6.png "Aad-create-app-step-6")

1. Après avoir confirmé votre sélection, `Access Kusto`choisissez des autorisations déléguées pour .

    ![texte de remplacement](./images/Aad-create-app-step-7.png "Aad-create-app-step-7")

1. Sélectionnez `Done` pour terminer le processus.



### <a name="2-set-permissions-to-the-application-on-kusto-cluster"></a>2. Définir les autorisations de l’application sur le cluster Kusto

> Avant d’utiliser votre nouvelle application provisionnée, vérifiez qu’elle se résout à partir de l’API graphique.<br>
    La propagation des modifications de l’AAD peut prendre jusqu’à plusieurs heures.

1. Accédez à votre administrateur de base de données pour accorder des autorisations à la nouvelle application.
Regardez la section [des directeurs de base de données de gestion](../security-roles.md) pour les détails sur la fixation des autorisations.<br>
Pour la mise en place des autorisations d’ingestion, voir [Autorisations d’ingestion](../../api/netfx/kusto-ingest-client-permissions.md).
1. Ajoutez une connexion à ce service dans votre Kusto Explorer, si vous ne l’avez pas encore.

### <a name="3-application-can-now-access-kusto"></a>3. L’application peut maintenant accéder à Kusto

Lorsque vous utilisez votre application nouvellement provisionnée pour accéder aux clusters Kusto à l’aide des bibliothèques clientes Kusto, spécifiez la chaîne de connexion suivante (si vous avez choisi de vous authentifier avec une clé partagée) :

`https://`*ClusterDnsName*`/;Federated Security=True;Application Client Id=`ApplicationClientId ApplicationKey ClusterDnsName ApplicationClientId ApplicationKey ClusterDnsName*ApplicationClientId*`;Application Key=`*ApplicationKey* ClusterD


### <a name="appendix-a-aad-errors"></a>Annexe A: Erreurs AAD

#### <a name="aad-error-aadsts650057"></a>Erreur AAD AADSTS650057

Si votre application est utilisée pour authentifier les utilisateurs ou les applications pour l’accès Kusto, vous devez configurer des autorisations déléguées pour l’application de service Kusto, c’est-à-dire déclarer que votre application peut authentifier les utilisateurs ou les applications pour l’accès Kusto.
Si vous ne le faites pas, une erreur semblable à la suivante se produit en cas de tentative d’authentification :

`AADSTS650057: Invalid resource. The client has requested access to a resource which is not listed in the requested permissions in the client's application registration...`

Vous devrez suivre les instructions sur la [mise en place des autorisations déléguées pour l’application de service Kusto](#set-up-delegated-permissions-for-kusto-service-application).

#### <a name="enable-user-consent-if-needed"></a>Activer le consentement de l’utilisateur si nécessaire

Votre administrateur locataire de l’AAD peut adopter une politique qui empêche les utilisateurs locataires de donner leur consentement aux demandes. Cela se traduira par une erreur similaire à ce qui suit, quand un utilisateur tente de se connecter à votre application:

`AADSTS65001: The user or administrator has not consented to use the application with ID '<App ID>' named 'App Name'`

Vous devrez demander à votre administrateur AAD d’accorder le consentement de tous les utilisateurs du locataire, ou d’activer le consentement de l’utilisateur pour votre demande spécifique.



### <a name="appendix-b-advanced-topics-and-troubleshooting"></a>Annexe B : Sujets avancés et dépannage

* Voir la documentation [sur les chaînes de connexion Kusto](../../api/connection-strings/kusto.md) pour la référence complète des chaînes de connexion supportées
