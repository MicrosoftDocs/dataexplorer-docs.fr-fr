---
title: Vue d’ensemble de Kusto Access Control - Azure Data Explorer | Microsoft Docs
description: Cet article décrit Kusto Access Control dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 11/25/2019
ms.openlocfilehash: ecdcdf22fe25c855045d90e294597c1abc769c03
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862103"
---
# <a name="kusto-access-control-overview"></a>Vue d’ensemble de Kusto Access Control

Access Control dans Kusto repose sur deux dimensions :
* [Authentification](#authentication) : Validation de l’identité du principal de sécurité effectuant une demande
* [Autorisation](#authorization) : Vérification que le principal de sécurité effectuant une demande est autorisé à l’effectuer sur la ressource cible

Pour pouvoir exécuter correctement une requête ou une commande de contrôle sur un cluster, une base de données ou une table Kusto, l’acteur doit satisfaire à la fois à l’authentification et à l’autorisation.

## <a name="authentication"></a>Authentification


**Azure Active Directory (AAD)** est le service d’annuaire cloud multilocataire de prédilection d’Azure. Il est capable d’authentifier les principaux de sécurité ou de se fédérer avec d’autres fournisseurs d’identité, comme Active Directory de Microsoft.

AAD est la méthode recommandée pour l’authentification auprès de Kusto dans Microsoft. Plusieurs scénarios d’authentification sont ainsi pris en charge :
* **Authentification de l’utilisateur** (ouverture de session interactive) : utilisée pour authentifier les principaux humains.
* **Authentification de l’application** (ouverture de session non interactive) : utilisée pour authentifier les services et les applications qui doivent s’exécuter/s’authentifier en l’absence d’utilisateur humain.

### <a name="user-authentication"></a>Authentification utilisateur
L’authentification utilisateur se produit lorsque l’utilisateur présente des informations d’identification à AAD (ou à un fournisseur d’identité qui travaille avec AAD, comme ADFS) et reçoit un jeton de sécurité qu’il peut présenter au service Kusto. Le service Kusto ne se soucie pas de la façon dont le jeton de sécurité a été obtenu, il s’attache à déterminer si le jeton est valide et quelles informations y place AAD (ou le fournisseur d’identité fédéré).

Côté client, Kusto prend en charge l’authentification interactive, dans laquelle la bibliothèque de client AAD ADAL ou du code similaire demandent à l’utilisateur d’entrer des informations d’identification. Il prend également en charge l’authentification basée sur un jeton, dans laquelle l’application qui utilise Kusto obtient un jeton utilisateur valide et le présente. En dernier lieu, il prend en charge un scénario dans lequel l’application qui utilise Kusto obtient un jeton utilisateur valide pour un autre service (autre que Kusto), à condition qu’il existe une relation d’approbation entre cette ressource et Kusto.

Pour plus d’informations sur l’utilisation des bibliothèques de clients Kusto et l’authentification avec AAD auprès de Kusto, consultez les [chaînes de connexion Kusto](../../api/connection-strings/kusto.md).

### <a name="application-authentication"></a>Authentification de l’application
Quand les demandes ne sont associées à aucun utilisateur spécifique ou qu’aucun utilisateur n’est disponible pour entrer des informations d’identification, le flux d’authentification de l’application AAD peut être utilisé. Dans ce flux, l’application s’authentifie auprès d’AAD (ou du fournisseur d’identité fédéré) en présentant des informations secrètes. Les scénarios suivants sont pris en charge par les différents clients Kusto :

* Authentification de l’application à l’aide d’un certificat X.509v2 installé localement.
* Authentification de l’application à l’aide d’un certificat X.509v2 donné à la bibliothèque de client sous la forme d’un flux d’octets.
* Authentification de l’application à l’aide d’un ID d’application AAD et d’une clé d’application AAD (l’équivalent de l’authentification avec un nom d’utilisateur/mot de passe pour les applications).
* Authentification de l’application à l’aide d’un jeton AAD valide précédemment obtenu (délivré à Kusto).
* Authentification d’application à l’aide d’un jeton AAD valide précédemment obtenu, délivré à une autre ressource, à condition qu’il existe une relation d’approbation entre cette ressource et Kusto.


### <a name="microsoft-accounts-msas"></a>Comptes Microsoft (MSA)
Les comptes Microsoft (MSA) désignent tous les comptes d’utilisateur non organisationnels gérés par Microsoft, par exemple, `hotmail.com`, `live.com`, `outlook.com`.
Kusto prend en charge l’authentification de l’utilisateur pour les MSA (notez qu’il n’existe aucun concept de groupe de sécurité), identifiés par leur UPN (nom d’utilisateur principal).
Quand un principal MSA est configuré sur une ressource Kusto, Kusto ne tente **pas** de résoudre l’UPN fourni.

### <a name="authenticated-sdk-or-rest-calls"></a>Appels SDK ou REST authentifiés
* Quand vous utilisez l’API REST, l’authentification s’effectue à l’aide de l’en-tête `Authorization` HTTP standard.
* Quand vous utilisez l’une des bibliothèques .NET Kusto, l’authentification est contrôlée en spécifiant la méthode d’authentification et les paramètres dans la [chaîne de connexion Kusto](../../api/connection-strings/kusto.md) ou en définissant des propriétés sur l’objet [ClientRequestProperties](https://kusto.azurewebsites.net/docs/api/request-properties.html).

### <a name="kusto-client-sdk-as-an-aad-client-application"></a>SDK client Kusto comme application cliente AAD
Quand les bibliothèques de clients Kusto appellent ADAL (la bibliothèque de client AAD) pour obtenir un jeton afin de communiquer avec Kusto, les informations suivantes sont fournies :

1. Ressource (URI de cluster comme `https://Cluster-and-region.kusto.windows.net`)
2. ID d’application cliente AAD
3. URI de redirection de l’application cliente AAD
4. Locataire AAD (Cela affecte le point de terminaison AAD utilisé pour l’authentification, par exemple, pour le locataire AAD `microsoft.com`, le point de terminaison AAD serait `https://login.microsoftonline.com/microsoft.com`.)

Le jeton retourné par ADAL à la bibliothèque de client Kusto a l’URL de cluster Kusto appropriée comme audience et l’autorisation « Accès Kusto » comme étendue.

**Exemple : obtenir un jeton utilisateur AAD pour un cluster Kusto**
```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD TenantID or name}");

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

Tous les principaux authentifiés, quelle que soit la méthode utilisée pour l’authentification, sont également soumis à une vérification de l’autorisation avant d’être autorisés à effectuer une action sur une ressource Kusto.
Kusto utilise un [modèle d’autorisation basé sur les rôles](role-based-authorization.md) : les principaux sont affectés à un ou plusieurs **rôles de sécurité**, et l’autorisation réussit tant que l’un des rôles du principal est autorisé.

Par exemple, le **rôle d’utilisateur de base de données** accorde aux principaux de sécurité (utilisateurs ou services) le droit de lire les données d’une base de données particulière, de créer des tables dans la base de données, ainsi que d’y créer des fonctions.

L’association des principaux de sécurité à des rôles de sécurité peut se définir individuellement ou à l’aide de groupes de sécurité (définis dans AAD). Les commandes individuelles correspondantes sont définies dans [Définition des règles d’autorisation basées sur les rôles](../security-roles.md).
