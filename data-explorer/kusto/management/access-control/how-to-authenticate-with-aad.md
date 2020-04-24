---
title: How-To Authenticate avec AAD pour Azure Data Explorer Access - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit How-To Authenticate avec AAD pour Azure Data Explorer Access dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/13/2019
ms.openlocfilehash: dc3a87a290ac535ac475af5017170a0478cd9b54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522914"
---
# <a name="how-to-authenticate-with-aad-for-azure-data-explorer-access"></a>How-To Authenticate avec AAD pour Azure Data Explorer Access

La façon recommandée d’accéder à Azure Data Explorer est d’authentifier le service **d’annuaire actif Azure** (parfois aussi appelé **Azure AD**, ou tout simplement **AAD**). Cela garantit qu’Azure Data Explorer ne voit jamais les informations d’identification du directeur d’accès, en utilisant un processus en deux étapes :

1. Dans la première étape, le client communique avec le service AAD, s’authentifie et demande un jeton d’accès émis spécifiquement pour le point de terminaison Azure Data Explorer particulier que le client a l’intention d’accéder.
2. Dans la deuxième étape, le client émet des demandes à Azure Data Explorer, fournissant le jeton d’accès acquis dans la première étape comme une preuve d’identité à Azure Data Explorer.

Azure Data Explorer exécute ensuite la demande au nom du principal de sécurité pour lequel AAD a émis le jeton d’accès, et toutes les vérifications d’autorisation sont effectuées en utilisant cette identité.

Dans la plupart des cas, la recommandation est d’utiliser l’un des SDK Azure Data Explorer pour accéder au service programmatiquement, car ils suppriment une grande partie des tracas de la mise en œuvre du flux ci-dessus (et bien d’autres). Voir, par exemple, le [.NET SDK](../../api/netfx/about-the-sdk.md).
Les propriétés d’authentification sont ensuite définies par la [chaîne de connexion Kusto](../../api/connection-strings/kusto.md).
Si cela n’est pas possible, veuillez lire la suite pour obtenir des informations détaillées sur la façon de mettre en œuvre ce flux vous-même.

Les principaux scénarios d’authentification sont les :

* **Une application client authentifiant un utilisateur connecté**.
  Dans ce scénario, une application interactive (client) déclenche une invite AAD à l’utilisateur pour les informations d’identification (comme le nom d’utilisateur et le mot de passe).
  Voir [l’authentification de l’utilisateur](#user-authentication),

* **Une application " sans tête "sans tête".**
  Dans ce scénario, une application est en cours d’exécution sans utilisateur présent pour fournir des informations d’identification, et au lieu de cela l’application authentifie comme "lui-même" à AAD en utilisant certaines informations d’identification, il a été configuré avec.
  Voir [l’authentification de l’application](#application-authentication).

* **Au nom de l’authentification**.
  Dans ce scénario, parfois appelé le scénario "service web" ou "application web", l’application obtient un jeton d’accès AAD à partir d’une autre application, puis le "convertit" en un autre jeton d’accès AAD qui peut être utilisé avec Azure Data Explorer.
  En d’autres termes, l’application agit en tant que médiateur entre l’utilisateur ou l’application qui a fourni des informations d’identification et le service Azure Data Explorer.
  Voir [au nom de l’authentification](#on-behalf-of-authentication).

## <a name="specifying-the-aad-resource-for-azure-data-explorer"></a>Spécifier la ressource AAD pour Azure Data Explorer

Lors de l’acquisition d’un jeton d’accès auprès d’AAD, le client doit indiquer à AAD à quelle **ressource AAD** le jeton doit être délivré. La ressource AAD d’un point de terminaison Azure Data Explorer est l’URI du point de terminaison, à moins que les informations portuaires et le chemin. Par exemple :

```txt
https://help.kusto.windows.net
```



## <a name="specifying-the-aad-tenant-id"></a>Spécifier l’ID du locataire de l’AAD

AAD est un service multi-locataires, et chaque organisation peut créer un objet appelé **répertoire** dans AAD. L’objet d’annuaire contient des objets liés à la sécurité tels que des comptes d’utilisateurs, des applications et des groupes. AAD se réfère souvent à l’annuaire comme un **locataire**. Les locataires AAD sont identifiés par un GUID **(ID locataire**). Dans de nombreux cas, les locataires de l’AAD peuvent également être identifiés par le nom de domaine de l’organisation.

Par exemple, une organisation appelée «Contoso» `4da81d62-e0a8-4899-adad-4349ca6bfe24` peut avoir `contoso.com`la pièce d’identité du locataire et le nom de domaine .

## <a name="specifying-the-aad-authority"></a>Spécifier l’autorité AAD

AAD a un certain nombre de critères d’évaluation pour l’authentification:

* Lorsque le locataire hébergeant le mandant étant authentifié est connu (en d’autres termes, quand `https://login.microsoftonline.com/{tenantId}`on sait dans quel répertoire AAD l’utilisateur ou l’application sont), le critère d’évaluation AAD est .
  `{tenantId}` Voici soit l’id de locataire de l’organisation dans AAD, `contoso.com`soit son nom de domaine (p. ex. ).

* Lorsque le locataire hébergeant le mandant étant authentifié n’est `{tenantId}` pas connu, `common`le point de terminaison « commun » peut être utilisé en remplaçant ce qui précède par la valeur .

> [!NOTE]
> Le critère d’évaluation AAD utilisé pour l’authentification est également appelé **URL d’autorité AAD** ou simplement **autorité AAD**.

## <a name="aad-token-cache"></a>Cache symbolique AAD

Lors de l’utilisation de l’Azure Data Explorer SDK, les jetons AAD sont stockés sur la machine locale dans un cache symbolique par utilisateur (un fichier appelé **%APPDATA%-Kusto-tokenCache.data** qui ne peut être consulté ou décrypté par l’utilisateur connecté.) Le cache est inspecté pour les jetons avant d’inciter l’utilisateur pour les informations d’identification, réduisant ainsi considérablement le nombre de fois qu’un utilisateur doit entrer des informations d’identification.

> [!NOTE]
> Le cache symbolique AAD réduit le nombre d’invites interactives qu’un utilisateur serait présenté avec l’accès Azure Data Explorer, mais ne les réduit pas complet. En outre, les utilisateurs ne peuvent pas anticiper à l’avance quand ils seront invités pour les informations d’identification.
> Cela signifie qu’il ne faut pas essayer d’utiliser un compte utilisateur pour accéder à Azure Data Explorer s’il est nécessaire de prendre en charge les connexions non interactives (comme lors de la planification des tâches par exemple), parce que lorsque vient le temps d’inciter l’utilisateur connecté pour les informations d’identification qui invite échoueront si l’exécution sous logon non interactif.


## <a name="user-authentication"></a>Authentification utilisateur

La façon la plus simple d’accéder à Azure Data Explorer avec l’authentification de l’utilisateur est d’utiliser l’Azure Data Explorer SDK et de définir la `Federated Authentication` propriété de la chaîne de connexion Azure Data Explorer à `true`. La première fois que le SDK est utilisé pour envoyer une demande au service, l’utilisateur sera présenté avec un formulaire de connexion pour entrer les informations d’identification AAD, et sur l’authentification réussie de la demande sera envoyée.

Les applications qui n’utilisent pas l’Azure Data Explorer SDK peuvent toujours utiliser la bibliothèque cliente AAD (ADAL) au lieu de mettre en œuvre le client du protocole de sécurité du service AAD. S’ilhttps://github.com/AzureADSamples/WebApp-WebAPI-OpenIDConnect-DotNetvous plaît voir [ ] par exemple de le faire à partir d’une application .NET.

Pour authentifier les utilisateurs pour l’accès Azure Data Explorer, une application doit d’abord être accordée à l’autorisation `Access Kusto` déléguée. S’il vous plaît voir [Kusto guide pour les demandes AAD provisionner](how-to-provision-aad-app.md#set-up-delegated-permissions-for-kusto-service-application) pour plus de détails.

Le bref extrait de code suivant démontre l’utilisation d’ADAL pour acquérir un jeton utilisateur AAD pour accéder à Azure Data Explorer (lance l’interface utilisateur logon):

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

Le bref extrait de code suivant démontre l’utilisation d’ADAL pour acquérir un jeton d’application AAD pour accéder à Azure Data Explorer. Dans ce flux, aucune invite n’est présentée, et l’application doit être enregistrée auprès d’AAD et équipée d’informations d’identification nécessaires pour effectuer l’authentification de l’application (comme une clé d’application émise par AAD, ou un certificat X509v2 qui a été pré-enregistré auprès d’AAD).

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

## <a name="on-behalf-of-authentication"></a>Au nom de l’authentification

Dans ce scénario, une demande a été envoyée un jeton d’accès AAD pour une ressource arbitraire gérée par l’application, et elle utilise ce jeton pour acquérir un nouveau jeton d’accès AAD pour la ressource Azure Data Explorer afin que l’application puisse accéder à Kusto au nom du principal indiqué par le jeton d’accès AAD original.

Ce flux est appelé le [flux d’échange de jetons OAuth2](https://tools.ietf.org/html/draft-ietf-oauth-token-exchange-04).
Il nécessite généralement plusieurs étapes de configuration avec AAD, et dans certains cas (selon la configuration du locataire AAD) peut exiger le consentement spécial de l’administrateur du locataire AAD.



**Étape 1 : Établir une relation de confiance entre votre application et le service Azure Data Explorer**

1. Ouvrez le [portail Azure](https://portal.azure.com/) et assurez-vous que vous êtes connecté au bon locataire (voir coin supérieur/droit pour l’identité utilisée pour vous connecter au portail).

2. Sur le volet ressources, cliquez sur **Azure Active Directory**, puis **les enregistrements d’applications**.

3. Localiser l’application qui utilise le flux au nom et l’ouvrir.

4. Cliquez sur **les autorisations API**, puis **ajouter une permission**.

5. Recherchez l’application **baptisée Azure Data Explorer** et sélectionnez-la.

6. Sélectionnez **user_impersonation / Access Kusto**.

7. Cliquez **sur Ajouter la permission**.

**Étape 2 : Effectuez l’échange de jetons dans le code de votre serveur**

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

**Étape 3 : Fournir le jeton à la bibliothèque cliente de Kusto et exécuter des requêtes**

```csharp
var kcsb = new KustoConnectionStringBuilder(string.Format(
    "https://{0}.kusto.windows.net;fed=true;UserToken={1}", 
    clusterName, 
    tokenForKusto.AccessToken));
var client = KustoClientFactory.CreateCslQueryProvider(kcsb);
var queryResult = client.ExecuteQuery(databaseName, query, null);
```

## <a name="web-client-javascript-authentication-and-authorization"></a>Authentification et autorisation du client Web (JavaScript)



**Configuration d’application AAD**

> [!NOTE]
> En plus des [étapes](./how-to-provision-aad-app.md) standard que vous devez suivre afin de configurer une application AAD, vous devez également activer le flux implicite oauth dans votre application AAD. Vous pouvez y parvenir en sélectionnant le manifeste à partir de votre page d’application dans le portail azur, et définir oauth2AllowImplicitFlow à vrai.

**Détails**

Lorsque le client est un code JavaScript exécutant dans le navigateur de l’utilisateur, le flux implicite de subvention est utilisé. Le jeton accordant à l’application client l’accès au service Azure Data Explorer est fourni immédiatement après une authentification réussie dans le cadre de la redirection URI (dans un fragment URI); aucun jeton de rafraîchissement n’est donné dans ce flux, de sorte que le client ne peut pas mettre en cache le jeton pendant de longues périodes de temps et le réutiliser.

Comme dans le flux de clients natifs, il devrait y avoir deux applications AAD (Server et Client) avec une relation configurée entre eux. 

AdalJs nécessite d’obtenir un id_token avant toute access_token appels sont effectués.

Le jeton d’accès `AuthenticationContext.login()` est obtenu en appelant la méthode, `Authenticationcontext.acquireToken()`et access_tokens sont obtenus en appelant .

* Créez un AuthenticationContext avec la bonne configuration :

```javascript
var config = {
    tenant: "microsoft.com",
    clientId: "<Web AAD app with current website as reply URL. for example, KusDash uses f86897ce-895e-44d3-91a6-aaeae54e278c>",
    redirectUri: "<where you'd like AAD to redirect after authentication succeeds (or fails).>",
    postLogoutRedirectUri: "where you'd like AAD to redirect the browser after logout."
};

var authContext = new AuthenticationContext(config);
```

* Appelez `authContext.login()` avant `acquireToken()` d’essayer si vous n’êtes pas connecté. une bonne façon ot savoir si vous êtes connecté `authContext.getCachedUser()` ou non `false`est d’appeler et de voir si elle revient )
* Appelez `authContext.handleWindowCallback()` chaque fois que votre page se charge. Il s’agit de la pièce de code qui intercepte la redirection de retour d’AAD et tire le jeton hors de l’URL fragment et le cache.
* Appelez `authContext.acquireToken()` pour obtenir le jeton d’accès réel, maintenant que vous avez un jeton d’identification valide. Le premier paramètre à acquérirToken sera l’URL de ressources d’application AAD serveur Kusto.  

```javascript
 authContext.acquireToken("<Kusto cluster URL>", callbackThatUsesTheToken);
 ```

* dans le rappelCesLes ImagesTheToken vous pouvez utiliser le jeton comme un jeton porteur dans la demande Azure Data Explorer. Par exemple :

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

> Avertissement - si vous obtenez l’exception suivante ou similaire lors de l’authentification:`ReferenceError: AuthenticationContext is not defined` 
c’est probablement parce que vous n’avez pas AuthenticationContext dans l’espace de nom global. Malheureusement, AdalJS a actuellement une exigence sans papiers que le contexte d’authentification sera défini dans l’espace de nom global.