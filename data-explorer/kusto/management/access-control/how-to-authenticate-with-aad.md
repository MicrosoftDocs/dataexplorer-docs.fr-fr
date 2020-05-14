---
title: Kusto s’authentifier auprès d’AAD pour l’accès-Azure Explorateur de données
description: Cet article décrit comment s’authentifier avec AAD pour l’accès Explorateur de données Azure dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 09/13/2019
ms.openlocfilehash: 34a0e5cd7107827cd97eb0baf9a3d40b408b2024
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382070"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>Comment s’authentifier auprès d’AAD pour l’accès Explorateur de données Azure

La méthode recommandée pour accéder à Azure Explorateur de données consiste à s’authentifier auprès du service **Azure Active Directory** (parfois également appelé **Azure ad**ou simplement **AAD**). Cela garantit qu’Azure Explorateur de données ne voit jamais l’accès aux informations d’identification de l’annuaire du principal, à l’aide d’un processus en deux étapes :

1. Dans la première étape, le client communique avec le service AAD, s’authentifie auprès de lui et demande un jeton d’accès émis spécifiquement pour le point de terminaison de Explorateur de données Azure spécifique auquel le client a l’intention d’accéder.
2. Dans la deuxième étape, le client émet des demandes à Azure Explorateur de données, en fournissant le jeton d’accès acquis lors de la première étape comme preuve d’identité à Azure Explorateur de données.

Azure Explorateur de données exécute ensuite la demande pour le compte du principal de sécurité pour lequel AAD a émis le jeton d’accès, et toutes les vérifications d’autorisation sont effectuées à l’aide de cette identité.

Dans la plupart des cas, il est recommandé d’utiliser l’un des kits de développement logiciel (SDK) Azure Explorateur de données pour accéder au service par programme, car ils suppriment la majeure partie de la contrainte de mise en œuvre du fluide ci-dessus (et bien plus encore). Consultez, par exemple, le [Kit de développement logiciel (SDK) .net](../../api/netfx/about-the-sdk.md).
Les propriétés d’authentification sont ensuite définies par la [chaîne de connexion Kusto](../../api/connection-strings/kusto.md).
Si ce n’est pas possible, consultez la rubrique pour plus d’informations sur la façon de mettre en œuvre ce processus vous-même.

Les principaux scénarios d’authentification sont les suivants :

* **Application cliente authentifiant un utilisateur connecté**.
  Dans ce scénario, une application interactive (client) déclenche une invite AAD à l’utilisateur pour les informations d’identification (telles que le nom d’utilisateur et le mot de passe).
  Voir [authentification utilisateur](#user-authentication),

* **Une application « headless »**.
  Dans ce scénario, une application s’exécute sans que l’utilisateur soit présent pour fournir les informations d’identification, et à la place, l’application s’authentifie comme « elle-même » sur AAD en utilisant des informations d’identification avec lesquelles elle a été configurée.
  Voir [authentification de l’application](#application-authentication).

* **Pour le compte de l’authentification**.
  Dans ce scénario, parfois appelé « service Web » ou « application Web », l’application obtient un jeton d’accès AAD d’une autre application, puis la « convertit » en un autre jeton d’accès AAD qui peut être utilisé avec Azure Explorateur de données.
  En d’autres termes, l’application agit comme un médiateur entre l’utilisateur ou l’application qui a fourni les informations d’identification et le service Azure Explorateur de données.
  Consultez [l’authentification pour le compte de](#on-behalf-of-authentication).

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Spécification de la ressource AAD pour Azure Explorateur de données

Lors de l’acquisition d’un jeton d’accès à partir d’AAD, le client doit indiquer à AAD la **ressource AAD** à laquelle le jeton doit être émis. La ressource AAD d’un point de terminaison de Explorateur de données Azure est l’URI du point de terminaison, en excluant les informations de port et le chemin d’accès. Par exemple :

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>Spécification de l’ID de locataire AAD

AAD est un service multi-locataire, et chaque organisation peut créer un objet nommé **Directory** dans AAD. L’objet d’annuaire contient des objets liés à la sécurité tels que les comptes d’utilisateurs, les applications et les groupes. AAD fait souvent référence au répertoire en tant que **locataire**. Les locataires AAD sont identifiés par un GUID (**ID de locataire**). Dans de nombreux cas, les locataires AAD peuvent également être identifiés par le nom de domaine de l’organisation.

Par exemple, une organisation appelée « Contoso » peut avoir l’ID `4da81d62-e0a8-4899-adad-4349ca6bfe24` de locataire et le nom de domaine `contoso.com` .

## <a name="specifying-the-aad-authority"></a>Spécification de l’autorité AAD

AAD possède un certain nombre de points de terminaison pour l’authentification :

* Lorsque le locataire hébergeant le principal en cours d’authentification est connu (en d’autres termes, lorsqu’il connaît le répertoire AAD dans lequel se trouve l’utilisateur ou l’application), le point de terminaison AAD est `https://login.microsoftonline.com/{tenantId}` .
  Ici, `{tenantId}` est soit l’ID de locataire de l’organisation dans AAD, soit son nom de domaine (par exemple, `contoso.com` ).

* Lorsque le locataire qui héberge le principal en cours d’authentification n’est pas connu, le point de terminaison « commun » peut être utilisé en remplaçant la `{tenantId}` valeur ci-dessus par la valeur `common` .

> [!NOTE]
> Le point de terminaison AAD utilisé pour l’authentification est également appelé URL de l' **autorité AAD** ou simplement **autorité AAD**.

## <a name="aad-token-cache"></a>Cache de jeton AAD

Lorsque vous utilisez le kit de développement logiciel (SDK) Azure Explorateur de données, les jetons AAD sont stockés sur l’ordinateur local dans un cache de jeton par utilisateur (un fichier appelé **%AppData%\Kusto\tokenCache.Data** qui est uniquement accessible ou déchiffré par l’utilisateur connecté). Le cache fait l’inspection des jetons avant d’inviter l’utilisateur à entrer des informations d’identification, ce qui réduit considérablement le nombre de fois où un utilisateur doit entrer ses informations d’identification.

> [!NOTE]
> Le cache de jeton AAD réduit le nombre d’invites interactives qu’un utilisateur peut obtenir pour accéder à Azure Explorateur de données, mais ne les réduit pas. En outre, les utilisateurs ne peuvent pas anticiper à l’avance lorsqu’ils sont invités à saisir leurs informations d’identification.
> Cela signifie qu’il ne doit pas tenter d’utiliser un compte d’utilisateur pour accéder à Azure Explorateur de données s’il est nécessaire de prendre en charge des ouvertures de session non interactives (par exemple, lors de la planification des tâches, par exemple), car lorsque le temps est écoulé pour demander à l’utilisateur connecté les informations d’identification qui s’exécutent en cas d’ouverture de session non interactive.


## <a name="user-authentication"></a>Authentification utilisateur

Le moyen le plus simple d’accéder à Azure Explorateur de données avec l’authentification des utilisateurs consiste à utiliser le kit de développement logiciel (SDK) Azure Explorateur de données et à définir la `Federated Authentication` propriété de la chaîne de connexion azure Explorateur de données sur `true` . La première fois que le kit de développement logiciel (SDK) est utilisé pour envoyer une demande au service, un formulaire de connexion est présenté à l’utilisateur pour entrer les informations d’identification AAD et, lors de l’authentification réussie, la demande est envoyée.

Les applications qui n’utilisent pas le kit de développement logiciel (SDK) Azure Explorateur de données peuvent toujours utiliser la bibliothèque cliente AAD (ADAL) au lieu d’implémenter le client du protocole de sécurité du service AAD. https://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNetPour obtenir un exemple de cette procédure à partir d’une application .net, consultez [].

Pour authentifier les utilisateurs pour l’accès à Azure Explorateur de données, une application doit d’abord se voir accorder l' `Access Kusto` autorisation déléguée. Pour plus d’informations, consultez le [Guide Kusto pour l’approvisionnement d’applications AAD](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application) .

L’extrait de code suivant illustre l’utilisation de ADAL pour acquérir un jeton d’utilisateur AAD pour accéder à Azure Explorateur de données (lance l’interface utilisateur d’ouverture de session) :

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire user token for the interactive user for Kusto:
AuthenticationResult result = authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", "your client app id",
    new Uri("your client app resource id"), new PlatformParameters(PromptBehavior.Auto)).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="application-authentication"></a>Authentification de l’application

Le court extrait de code suivant illustre l’utilisation de ADAL pour acquérir un jeton d’application AAD pour accéder à Azure Explorateur de données. Dans ce Flow, aucune invite ne s’affiche et l’application doit être inscrite auprès d’AAD et équipée des informations d’identification nécessaires pour effectuer l’authentification de l’application (par exemple, une clé d’application émise par AAD ou un certificat X509v2 qui a été préalablement inscrit auprès d’AAD).

```csharp
// Create an HTTP request
WebRequest request = WebRequest.Create(new Uri("https://{serviceNameAndRegion}.kusto.windows.net"));

// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Acquire application token for Kusto:
ClientCredential applicationCredentials = new ClientCredential("your application client ID", "your application key");
AuthenticationResult result =
        authContext.AcquireTokenAsync("https://{serviceNameAndRegion}.kusto.windows.net", applicationCredentials).GetAwaiter().GetResult();

// Extract Bearer access token and set the Authorization header on your request:
string bearerToken = result.AccessToken;
request.Headers.Set(HttpRequestHeader.Authorization, string.Format(CultureInfo.InvariantCulture, "{0} {1}", "Bearer", bearerToken));
```

## <a name="on-behalf-of-authentication"></a>Authentification pour le compte de

Dans ce scénario, une application a reçu un jeton d’accès AAD pour une ressource arbitraire gérée par l’application et utilise ce jeton pour acquérir un nouveau jeton d’accès AAD pour la ressource Azure Explorateur de données, afin que l’application puisse accéder à Kusto pour le compte du principal indiqué par le jeton d’accès AAD d’origine.

Ce Flow est appelé le [Flow OAuth2 Token Exchange](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04).
Elle nécessite généralement plusieurs étapes de configuration avec AAD, et dans certains cas (selon la configuration du locataire AAD) peut nécessiter un consentement particulier de l’administrateur du locataire AAD.



**Étape 1 : établir une relation d’approbation entre votre application et le service Azure Explorateur de données**

1. Ouvrez la [portail Azure](https://portal.azure.com/) et assurez-vous que vous êtes connecté au locataire approprié (voir le coin supérieur/droit de l’identité utilisée pour se connecter au portail).

2. Dans le volet ressources, cliquez sur **Azure Active Directory**, puis **inscriptions d’applications**.

3. Localisez l’application qui utilise le Flow pour le compte de et ouvrez-la.

4. Cliquez sur **autorisations d’API**, puis sur **Ajouter une autorisation**.

5. Recherchez l’application nommée **Azure Explorateur de données** , puis sélectionnez-la.

6. Sélectionnez **user_impersonation/accès Kusto**.

7. Cliquez sur **Ajouter une autorisation**.

**Étape 2 : effectuer l’échange de jetons dans votre code serveur**

```csharp
// Create Auth Context for AAD (common or tenant-specific endpoint):
AuthenticationContext authContext = new AuthenticationContext("AAD Authority URL");

// Exchange your token for for Kusto token.
// You will need to provide your application's client ID and secret to authenticate your application
var tokenForKusto = authContext.AcquireTokenAsync(
    "https://{serviceNameAndRegion}.kusto.windows.net",
    new ClientCredential(customerAadWebApplicationClientId, customerAAdWebApplicationSecret),
    new UserAssertion(customerAadWebApplicationToken)).GetAwaiter().GetResult();
```

**Étape 3 : fournir le jeton à la bibliothèque cliente Kusto et exécuter des requêtes**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}",
    clusterName,
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>Authentification et autorisation du client Web (JavaScript)



**Configuration de l’application AAD**

> [!NOTE]
> En plus des [étapes](./how-to-provision-aad-app.md) standard que vous devez suivre pour configurer une application AAD, vous devez également activer le fluide implicite OAuth dans votre application AAD. Pour cela, sélectionnez manifeste à partir de la page de votre application dans le portail Azure, puis définissez oauth2AllowImplicitFlow sur true.

**Détails**

Lorsque le client est un code JavaScript qui s’exécute dans le navigateur de l’utilisateur, le workflow d’octroi implicite est utilisé. Le jeton accordant à l’application cliente l’accès au service Azure Explorateur de données est fourni immédiatement après une authentification réussie dans le cadre de l’URI de redirection (dans un fragment d’URI). aucun jeton d’actualisation n’étant fourni dans ce Flow, le client ne peut pas mettre en cache le jeton pendant des périodes prolongées et le réutiliser.

Comme dans le workflow Native Client, il doit y avoir deux applications AAD (serveur et client) avec une relation configurée entre elles.

AdalJs requiert l’obtention d’un id_token avant d’effectuer des appels de access_token.

Le jeton d’accès est obtenu en appelant la `AuthenticationContext.login()` méthode, et access_tokens sont obtenus en appelant `Authenticationcontext.acquireToken()` .

* Créez un AuthenticationContext avec la configuration appropriée :

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* Appelez `authContext.login()` avant d’essayer `acquireToken()` si vous n’êtes pas connecté. une bonne façon de savoir si vous êtes connecté ou non consiste à appeler `authContext.getCachedUser()` et à voir si elle retourne la valeur `false` )
* Appelez `authContext.handleWindowCallback()` chaque fois que votre page se charge. Il s’agit de la partie de code qui intercepte la redirection d’AAD et extrait le jeton de l’URL de fragment et le met en cache.
* Appelez `authContext.acquireToken()` pour obtenir le jeton d’accès réel, maintenant que vous avez un jeton d’ID valide. Le premier paramètre de acquireToken sera l’URL de la ressource d’application Kusto Server AAD.

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* dans le callbackThatUsesTheToken, vous pouvez utiliser le jeton en tant que jeton du porteur dans la demande Azure Explorateur de données. Par exemple :

```javascript
var settings = {
    url: "https://" + clusterAndRegionName + ".kusto.windows.net/v1/rest/query",
    type: "POST",
    data: JSON.stringify({
        "db": dbName,
        "csl": query,
        "properties": null
    }),
    contentType: "application/json; charset=utf-8",
    headers: { "Authorization": "Bearer " + token },
    dataType: "json",
    jsonp: false,
    success: function(data, textStatus, jqXHR) {
        if (successCallback !== undefined) {
            successCallback(data.Tables[0]);
        }

    },
    error: function(jqXHR, textStatus, errorThrown) {
        if (failureCallback !==  undefined) {
            failureCallback(textStatus, errorThrown, jqXHR.responseText);
        }

    },
};

$.ajax(settings).then(function(data) {/* do something wil the data */});
```

> AVERTISSEMENT : Si vous recevez l’exception suivante ou similaire lors de l’authentification :`ReferenceError: AuthenticationContext is not defined`
Cela est probablement dû au fait que vous n’avez pas de AuthenticationContext dans l’espace de noms global.
Malheureusement, AdalJS a actuellement une exigence non documentée selon laquelle le contexte d’authentification sera défini dans l’espace de noms global.