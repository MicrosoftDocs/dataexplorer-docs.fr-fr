---
title: Authentification Azure Active Directory (AAD)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’authentification Azure Active Directory (AAD) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 09/13/2019
ms.openlocfilehash: 17da89206af12e2e4f7d9867372c8babf0c4aea1
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862086"
---
# <a name="azure-active-directory-aad-authentication"></a>Authentification Azure Active Directory (AAD)

Azure Active Directory (AAD) est le service d’annuaire cloud multilocataire de prédilection d’Azure. Il est capable d’authentifier les principaux de sécurité ou de se fédérer avec d’autres fournisseurs d’identité, comme Active Directory de Microsoft.

AAD permet l’application de différents types (application Web, application de bureau Windows, applications universelles, applications mobiles, etc.) pour authentifier et utiliser uniformément les services Kusto.

AAD prend en charge un certain nombre de scénarios d’authentification.
Si un utilisateur est présent pendant l’authentification, il doit authentifier l’utilisateur auprès d’AAD par l’authentification de l’utilisateur AAD.
Dans certains cas, un service peut utiliser Kusto même si aucun utilisateur n’est présent de manière interactive. Dans ce cas, l’un doit authentifier l’application via l’utilisation d’un secret d’application, comme décrit dans authentification d’application AAD.

Les méthodes d’authentification suivantes sont prises en charge par Kusto en général, notamment via les bibliothèques .NET suivantes :

* Authentification utilisateur interactive : ce mode nécessite une interactivité, comme si nécessaire, l’interface utilisateur de connexion s’affiche.
* Authentification de l’utilisateur avec un jeton AAD existant précédemment émis pour Kusto
* Authentification d’application avec AppID et secret partagé
* Authentification de l’application avec un certificat X. 509v2 installé localement ou certificat fourni en ligne
* Authentification d’application avec un jeton AAD existant précédemment émis pour Kusto
* Authentification de l’utilisateur ou de l’application avec un jeton AAD émis pour une autre ressource, à condition qu’il existe une approbation entre cette ressource et Kusto

Pour obtenir des conseils et des exemples, consultez la référence sur les [chaînes de connexion Kusto](../../api/connection-strings/kusto.md) .

## <a name="user-authentication"></a>Authentification utilisateur

L’authentification de l’utilisateur se produit lorsque l’utilisateur présente des informations d’identification à AAD (ou à un fournisseur d’identité qui se fédère avec AAD, comme ADFS), puis obtient un jeton de sécurité qu’il peut présenter au service Kusto. Le service Kusto ne se soucie pas de la façon dont le jeton de sécurité a été obtenu, il s’attache à déterminer si le jeton est valide et quelles informations y place AAD (ou le fournisseur d’identité fédéré).

Côté client, Kusto prend en charge l’authentification interactive, dans laquelle la bibliothèque de client AAD ADAL ou du code similaire demandent à l’utilisateur d’entrer des informations d’identification. Il prend également en charge l’authentification basée sur un jeton, dans laquelle l’application qui utilise Kusto obtient un jeton utilisateur valide et le présente. En dernier lieu, il prend en charge un scénario dans lequel l’application qui utilise Kusto obtient un jeton utilisateur valide pour un autre service (autre que Kusto), à condition qu’il existe une relation d’approbation entre cette ressource et Kusto.

Pour plus d’informations sur l’utilisation des bibliothèques de clients Kusto et l’authentification avec AAD auprès de Kusto, consultez les [chaînes de connexion Kusto](../../api/connection-strings/kusto.md).

## <a name="application-authentication"></a>Authentification de l’application

Quand les demandes ne sont associées à aucun utilisateur spécifique ou qu’aucun utilisateur n’est disponible pour entrer des informations d’identification, le flux d’authentification de l’application AAD peut être utilisé. Dans ce flux, l’application s’authentifie auprès d’AAD (ou du fournisseur d’identité fédéré) en présentant des informations secrètes. Les scénarios suivants sont pris en charge par les différents clients Kusto :

* Authentification de l’application à l’aide d’un certificat X.509v2 installé localement.
* Authentification de l’application à l’aide d’un certificat X.509v2 donné à la bibliothèque de client sous la forme d’un flux d’octets.
* Authentification de l’application à l’aide d’un ID d’application AAD et d’une clé d’application AAD (l’équivalent de l’authentification avec un nom d’utilisateur/mot de passe pour les applications).
* Authentification de l’application à l’aide d’un jeton AAD valide précédemment obtenu (délivré à Kusto).
* Authentification d’application à l’aide d’un jeton AAD valide précédemment obtenu, délivré à une autre ressource, à condition qu’il existe une relation d’approbation entre cette ressource et Kusto.

## <a name="aad-server-application-permissions"></a>Autorisations de l’application serveur AAD

Dans le cas général, une application de serveur AAD peut définir plusieurs autorisations (par exemple, une autorisation de lecture seule et une autorisation de lecture-écriture) et l’application cliente AAD peut déterminer les autorisations dont elle a besoin lorsqu’elle demande un jeton d’autorisation. Dans le cadre de l’acquisition de jetons, l’utilisateur est invité à autoriser l’application cliente AAD à agir au nom de l’utilisateur avec l’autorisation de disposer de ces autorisations. Si l’utilisateur approuve, ces autorisations sont listées dans la revendication d’étendue du jeton émis pour l’application cliente AAD.



L’application cliente AAD est configurée pour demander l’autorisation « accès Kusto » à l’utilisateur (lequel AAD appelle le « propriétaire de la ressource »).

## <a name="kusto-client-sdk-as-an-aad-client-application"></a>SDK client Kusto comme application cliente AAD

Quand les bibliothèques de clients Kusto appellent ADAL (la bibliothèque de client AAD) pour obtenir un jeton afin de communiquer avec Kusto, les informations suivantes sont fournies :

1. Locataire AAD, tel qu’il est reçu de l’appelant
2. ID d’application cliente AAD
3. L’ID de ressource du client AAD
4. Le ReplyUrl AAD (URL que le service AAD redirige vers une fois l’authentification terminée ; ADAL capture ensuite cette redirection et en extrait le code d’autorisation.
5. URI du cluster («https://Cluster-and-region.kusto.windows.net»).

Le jeton retourné par ADAL à la bibliothèque cliente Kusto dispose de l’application de serveur Kusto AAD comme audience et de l’autorisation « accès Kusto » comme étendue.

## <a name="authenticating-with-aad-programmatically"></a>Authentification avec AAD par programmation

Les articles suivants expliquent comment s’authentifier par programmation auprès de Kusto avec AAD :

* [Comment approvisionner une application AAD](./how-to-provision-aad-app.md)
* [Comment effectuer une authentification AAD](./how-to-authenticate-with-aad.md)
