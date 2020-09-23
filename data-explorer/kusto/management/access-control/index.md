---
title: Vue d’ensemble de Kusto Access Control – Azure Data Explorer
description: Cet article décrit Kusto Access Control dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 11/25/2019
ms.openlocfilehash: 7b97d62e007b5294bf776fb5d5adcbac435056ef
ms.sourcegitcommit: 3fc8e9b6a313a863916031d4beba84123edcf123
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90847851"
---
# <a name="kusto-access-control-overview"></a>Vue d’ensemble de Kusto Access Control

Dans Azure Data Explorer, Access Control est basé sur deux facteurs clés.
* [Authentification](#authentication) : valide l’identité du principal de sécurité effectuant une demande
* [Autorisation](#authorization) : vérifie que le principal de sécurité effectuant une demande est autorisé à le faire sur la ressource cible

Une requête ou une commande de contrôle sur un cluster, une base de données ou une table Azure Data Explorer doit passer les contrôles d’authentification et d’autorisation avec succès.

## <a name="authentication"></a>Authentification

**Azure Active Directory (Azure AD)** est le service d’annuaire cloud multilocataire par défaut d’Azure. Il peut authentifier les principaux de sécurité ou se fédérer avec d’autres fournisseurs d’identité.

Azure AD est la méthode d’authentification par défaut de Microsoft Azure Data Explorer. Cette méthode prend en charge plusieurs scénarios d’authentification.
* **Authentification utilisateur** (connexion interactive) : utilisée pour authentifier les principaux humains.
* **Authentification d’application** (connexion non interactive) : utilisée pour authentifier les services et les applications qui doivent s’exécuter et s’authentifier sans l’intervention d’un utilisateur humain.

### <a name="user-authentication"></a>Authentification utilisateur

L’authentification utilisateur se produit quand l’utilisateur présente des informations d’identification à :
* Azure AD 
* Un fournisseur d’identité compatible avec Azure AD

En cas de succès, l’utilisateur reçoit un jeton de sécurité qui peut être présenté au service Azure Data Explorer. Le service Azure Data Explorer ne prête pas attention à la façon dont le jeton de sécurité est obtenu. En revanche, il se soucie de la validité du jeton et des informations qui y sont placées par Azure AD (ou le fournisseur d’identité fédéré).

Côté client, Azure Data Explorer prend en charge l’authentification interactive, où Microsoft Authentication Library (ou du code similaire) demande à l’utilisateur d’entrer des informations d’identification. Il prend aussi en charge l’authentification basée sur un jeton, où l’application qui utilise Azure Data Explorer obtient un jeton utilisateur valide. L’application qui utilise Azure Data Explorer peut aussi obtenir un jeton utilisateur valide pour un autre service. Le jeton utilisateur ne peut être obtenu que s’il existe une relation de confiance entre cette ressource et Azure Data Explorer.

Pour plus d’informations sur l’utilisation des bibliothèques de client Kusto et l’authentification à Azure Data Explorer avec Azure AD, consultez [Chaînes de connexion Kusto](../../api/connection-strings/kusto.md).

### <a name="application-authentication"></a>Authentification de l’application

Utilisez le flux d’authentification d’application Azure AD quand les demandes ne sont associées à aucun utilisateur spécifique ou qu’aucun utilisateur n’est disponible pour entrer des informations d’identification. Dans le flux, l’application s’authentifie auprès d’Azure AD (ou du fournisseur d’identité fédéré) en présentant des informations secrètes. Les scénarios suivants sont pris en charge par les différents clients Azure Data Explorer.

* Authentification d’application utilisant un certificat X.509v2 installé localement.
* Authentification d’application utilisant un certificat X.509v2 communiqué à la bibliothèque de client sous forme de flux d’octets.
* Authentification d’application utilisant un ID d’application Azure AD et une clé d’application Azure AD.

    > [!NOTE] 
    > L’ID et la clé sont l’équivalent d’un nom d’utilisateur et d’un mot de passe.

* Authentification d’application utilisant un jeton Azure AD valide obtenu précédemment, délivré à Azure Data Explorer.
* Authentification d’application utilisant un jeton Azure AD valide obtenu précédemment, délivré à une autre ressource. Cette méthode ne fonctionne que s’il existe une relation de confiance entre cette ressource et Azure Data Explorer.

### <a name="microsoft-accounts-msas"></a>Comptes Microsoft (MSA)

Un compte Microsoft (MSA) désigne tous les comptes d’utilisateurs non organisationnels gérés par Microsoft, comme `hotmail.com`, `live.com` et `outlook.com`.
Kusto prend en charge l’authentification utilisateur pour les comptes MSA (le concept de groupe de sécurité n’existe pas) qui sont identifiés par leur nom d’utilisateur principal (UPN).

Quand un principal MSA est configuré au niveau d’une ressource Azure Data Explorer, Azure Data Explorer **n’essaie pas** de résoudre le nom UPN fourni.

### <a name="authenticated-sdk-or-rest-calls"></a>Appels SDK ou REST authentifiés

* Quand l’API REST est utilisée, l’authentification s’effectue avec l’en-tête `Authorization` HTTP standard.
* Quand une des bibliothèques .NET Azure Data Explorer est utilisée, l’authentification est contrôlée en spécifiant la méthode et les paramètres d’authentification dans la [chaîne de connexion Kusto](../../api/connection-strings/kusto.md). Une autre méthode consiste à définir les propriétés au niveau de l’objet des [propriétés de demande du client](../../api/netfx/request-properties.md).

### <a name="azure-data-explorer-client-sdk-as-an-azure-ad-client-application"></a>SDK client Azure Data Explorer comme application cliente Azure AD

Quand les bibliothèques de client Kusto appellent Microsoft Authentification Library pour obtenir un jeton afin de communiquer avec Kusto, voici les informations qui sont fournies :

* La ressource (URI du cluster, par exemple `https://Cluster-and-region.kusto.windows.net`)
* L’ID de l’application cliente Azure AD
* L’URI de redirection de l’application cliente Azure AD
* Le locataire Azure AD qui affecte le point de terminaison Azure AD utilisé pour l’authentification. Par exemple, pour le locataire Azure AD `microsoft.com`, le point de terminaison Azure AD est `https://login.microsoftonline.com/microsoft.com`)

Le jeton que Microsoft Authentication Library retourne à la bibliothèque de client Azure Data Explorer présente l’URL de cluster Azure Data Explorer appropriée comme audience et l’autorisation « Accéder à Azure Data Explorer » comme étendue.

**Exemple : obtenir un jeton d’utilisateur Azure AD pour un cluster Azure Data Explorer**

```csharp
// Create Auth Context for Azure AD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD TenantID or name}");

// Provide your Application ID and redirect URI
var clientAppID = "{your client app id}";
var redirectUri = new Uri("{your client app redirect uri}");

// acquireTokenTask will receive the bearer token for the authenticated user
var acquireTokenTask = authContext.AcquireTokenAsync(
    $"https://{clusterNameAndRegion}.kusto.windows.net",
    clientAppID,
    redirectUri,
    new PlatformParameters(PromptBehavior.Auto, null)).GetAwaiter().GetResult();
```

## <a name="authorization"></a>Autorisation

Toutes les principaux authentifiés sont soumis à une vérification d’autorisation avant de pouvoir effectuer une action sur une ressource Azure Data Explorer.
Azure Data Explorer utilise un [modèle d’autorisation basé sur les rôles](role-based-authorization.md), où les principaux se voient attribuer un ou plusieurs rôles de sécurité. L’autorisation aboutit du moment qu’un des rôles du principal est autorisé.

Par exemple, le rôle d’utilisateur de base de données confère aux principaux de sécurité, utilisateurs ou services le droit de :
* lire les données d’une base de données déterminée
* créer des tables dans la base de données
* créer des fonctions dans la base de données

L’association des principaux de sécurité à des rôles de sécurité peut se définir individuellement ou à l’aide de groupes de sécurité définis dans Azure AD. Les commandes sont définies dans [Définition de règles d’autorisation basées sur des rôles](../security-roles.md).
