---
title: Azure Active Directory (AAD) Authentification - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Azure Active Directory (AAD) Authentication in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: 637252b91af53198b3ee494309857b2d4b6e6828
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522931"
---
# <a name="azure-active-directory-aad-authentication"></a>Azure Active Directory (AAD) Authentification

Azure Active Directory (AAD) est le service d’annuaire cloud multilocataire de prédilection d’Azure. Il est capable d’authentifier les principaux de sécurité ou de se fédérer avec d’autres fournisseurs d’identité, comme Active Directory de Microsoft.

AAD permet à l’application de différents types (application Web, application de bureau Windows, applications Universelles, applications mobiles, etc.) d’authentifier et d’utiliser uniformément les services Kusto.

AAD prend en charge un certain nombre de scénarios d’authentification.
S’il y a un utilisateur présent lors de l’authentification, il faut authentifier l’utilisateur à AAD par AAD User Authentication.
Dans certains cas, on veut un service pour utiliser Kusto même lorsqu’aucun utilisateur n’est présent de manière interactive. Dans de tels cas, il faut authentifier l’application par l’utilisation d’un secret d’application, tel que décrit dans AAD Application Authentication.

Les méthodes suivantes d’authentification sont prises en charge par Kusto en général, y compris à travers ses bibliothèques .NET:

* Authentification interactive de l’utilisateur - ce mode nécessite une interactivité, comme si nécessaire, l’interface utilisateur logon apparaîtra
* Authentification de l’utilisateur avec un jeton AAD existant précédemment émis pour Kusto
* Authentification de l’application avec AppID et secret partagé
* Authentification de l’application avec certificat ou certificat X.509v2 installé localement
* Authentification de l’application avec un jeton AAD existant précédemment émis pour Kusto
* Authentification de l’utilisateur ou de l’application avec un jeton AAD émis pour une autre ressource, à condition qu’il existe une confiance entre cette ressource et Kusto

S’il vous plaît voir la référence [des chaînes de connexion Kusto](../../api/connection-strings/kusto.md) pour les conseils et les exemples.

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

## <a name="aad-server-application-permissions"></a>Autorisations d’application de serveur AAD

Dans le cas général, une application serveur AAD peut définir plusieurs autorisations (p. ex., autorisation de lecture seulement et autorisation de lecture) et la demande de client AAD peut décider quelles autorisations il a besoin lorsqu’il demande un jeton d’autorisation. Dans le cadre de l’acquisition de jetons, l’utilisateur sera invité à autoriser l’application client AAD à agir au nom de l’utilisateur avec l’autorisation d’avoir ces autorisations. Si l’utilisateur approuve, ces autorisations seront énumérées dans la revendication de portée du jeton qui est délivrée à l’application client AAD.



L’application client AAD est configurée pour demander l’autorisation "Accès Kusto" de l’utilisateur (qu’AAD appelle "le propriétaire de la ressource").

## <a name="kusto-client-sdk-as-an-aad-client-application"></a>SDK client Kusto comme application cliente AAD

Quand les bibliothèques de clients Kusto appellent ADAL (la bibliothèque de client AAD) pour obtenir un jeton afin de communiquer avec Kusto, les informations suivantes sont fournies :

1. Le locataire de l’AAD, reçu de l’appelant
2. ID d’application cliente AAD
3. L’ID de ressources clientes AAD
4. L’AAD ReplyUrl (l’URL que le service AAD redirigera après l’authentification se termine avec succès; ADAL capture ensuite cette redirection et en extrait le code d’autorisation).
5. The Cluster URIhttps://Cluster-and-region.kusto.windows.net(' ').

Le jeton retourné par ADAL à la bibliothèque cliente de Kusto a l’application serveur Kusto AAD comme public, et la permission "Accès Kusto" comme portée.

## <a name="authenticating-with-aad-programmatically"></a>Authentification avec AAD Programmatically

Les articles suivants expliquent comment s’authentifier programmatiquement à Kusto avec AAD :

* [Comment fournir une demande AAD](./how-to-provision-aad-app.md)
* [Comment effectuer AAD Authentification](./how-to-authenticate-with-aad.md)

