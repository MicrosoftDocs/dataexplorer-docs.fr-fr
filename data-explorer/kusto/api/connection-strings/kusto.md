---
title: Chaînes de connexion Kusto-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les chaînes de connexion Kusto dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 8c5ade644f383a5a0d9e846b1a3143027d1eb467
ms.sourcegitcommit: b4d6c615252e7c7d20fafd99c5501cb0e9e2085b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83863181"
---
# <a name="kusto-connection-strings"></a>Chaînes de connexion Kusto

Les chaînes de connexion Kusto peuvent fournir les informations nécessaires à une application cliente Kusto pour établir une connexion à un point de terminaison de service Kusto. Les chaînes de connexion Kusto sont modélisées après les chaînes de connexion ADO.NET. Autrement dit, la chaîne de connexion est une liste délimitée par des points-virgules de paires de paramètres nom/valeur, éventuellement précédée d’un URI unique.

**Exemple :**

```text
https://help.kusto.windows.net/Samples; Fed=true; Accept=true
```

L’URI fournit le point de terminaison de service pour communiquer avec :

* ( `https://help.kusto.windows.net` ) : valeur de la `Data Source` propriété.
* `Samples`(base de données par défaut) : valeur de la `Initial Catalog` propriété.

Deux propriétés supplémentaires sont fournies à l’aide de la syntaxe nom/valeur : 

* `Fed`Property (également appelée `AAD Federated Security` ) a la valeur `true` .
* `Accept`propriété définie sur `true` .

> [!NOTE]
>
> * Les noms de propriétés ne respectent pas la casse et les espaces entre les paires nom/valeur sont ignorés.
> * Les valeurs de propriété **respectent** la casse. Une valeur de propriété qui contient un point-virgule ( `;` ), un guillemet simple ( `'` ) ou un guillemet double ( `"` ) doit être placée entre guillemets doubles.

Plusieurs outils clients Kusto prennent en charge une extension sur le préfixe URI de la chaîne de connexion, en ce qu’ils autorisent l’utilisation de la syntaxe abrégée `@` _ClusterName_ `/` _InitialCatalog_ .
Par exemple, la chaîne de connexion `@help/Samples` est traduite par ces outils en `https://help.kusto.windows.net/Samples; Fed=true` , qui indique trois propriétés ( `Data Source` , `Initial Catalog` et `AAD Federated Security` ).

Par programme, les chaînes de connexion Kusto peuvent être analysées et manipulées par la `Kusto.Data.KustoConnectionStringBuilder` classe C#. Cette classe valide toutes les chaînes de connexion et génère une exception au moment de l’exécution si la validation échoue.
Cette fonctionnalité est présente dans toutes les versions du kit de développement logiciel (SDK) Kusto.

## <a name="connection-string-properties"></a>Propriétés de la chaîne de connexion

Le tableau suivant répertorie toutes les propriétés que vous pouvez spécifier dans une chaîne de connexion Kusto.
Elle répertorie les noms de programmation (qui est le nom de la propriété dans l’objet), ainsi que les `Kusto.Data.KustoConnectionStringBuilder` noms de propriété supplémentaires qui sont des alias.

### <a name="general-properties"></a>Propriétés générales

| Nom de la propriété              | Autres noms                      | Nom de programmation  | Description                                                                                                                          |
|----------------------------|----------------------------------------|--------------------|---------------------------------------------------|
| Version du client pour le suivi |                                        | TraceClientVersion | Lors du suivi de la version du client, utilisez cette valeur   |
| source de données                | ADR, adresse, adresse réseau, serveur | DataSource         | URI spécifiant le point de terminaison de service Kusto. Par exemple, `https://mycluster.kusto.windows.net` ou `net.tcp://localhost`.               |
| Catalogue initial            | Base de données                               | InitialCatalog     | Nom de la base de données à utiliser par défaut. Par exemple, MyDatabase|
| Cohérence des requêtes          | QueryConsistency                       | QueryConsistency   | Définissez sur `strongconsistency` ou `weakconsistency` pour déterminer si la requête doit se synchroniser avec les métadonnées avant l’exécution. |

### <a name="user-authentication-properties"></a>Propriétés d’authentification de l’utilisateur

| Nom de la propriété          | Autres noms                          | Nom de programmation | Description                       |
|------------------------|--------------------------------------------|-------------------|-----------------------------------|
| Sécurité fédérée d’AAD | Sécurité fédérée, fédéré, FED, AADFed | FederatedSecurity | Valeur booléenne qui indique au client d’exécuter Azure active  |
| Authentification multifacteur            | MFA, EnforceMFA                             | EnforceMfa        | Valeur booléenne qui indique au client d’acquérir un jeton d’authentification multifacteur       |
| ID d'utilisateur                | UID, utilisateur                                  | UserID            | Valeur de chaîne qui indique au client d’effectuer l’authentification de l’utilisateur avec le nom d’utilisateur indiqué.           |
| Nom d’utilisateur pour le suivi  |                                            | TraceUserName     | Valeur de chaîne qui indique au service le nom d’utilisateur à utiliser lors du suivi de la demande en interne         |
| Jeton de l’utilisateur             | UsrToken, UserToken                        | UserToken         | Valeur de chaîne qui indique au client d’effectuer l’authentification de l’utilisateur avec le jeton de porteur spécifié.<br/>Remplace ApplicationClientId, ApplicationKey et ApplicationToken. (S’il est spécifié, ignore le workflow d’authentification du client réel en faveur du jeton fourni.)       |
| Espace de noms              | NS                                         | Espace de noms         | (Pour une utilisation ultérieure)  |



### <a name="application-authentication-properties"></a>Propriétés d’authentification de l’application

|Nom de la propriété                                     |Autres noms                         |Nom de programmation                             |Description      |
|--------------------------------------------------|------------------------------------------|----------------------------------------------|-----------------|
|Sécurité fédérée d’AAD                            |Sécurité fédérée, fédéré, FED, AADFed|FederatedSecurity                             |Valeur booléenne qui indique au client d’effectuer l’authentification fédérée Azure Active Directory (AAD).|
|Empreinte du certificat de l’application                |AppCert                                   |ApplicationCertificateThumbprint              |Valeur de chaîne qui fournit l’empreinte numérique du certificat client à utiliser lors de l’utilisation d’un workflow d’authentification de certificat client d’application.|
|ID client de l’application                             |AppClientId                               |ApplicationClientId                           |Valeur de chaîne qui fournit l’ID client de l’application à utiliser lors de l’authentification.|
|Clé de l’application                                   |AppKey                                    |ApplicationKey                                |Valeur de chaîne qui fournit la clé d’application à utiliser lors de l’authentification à l’aide d’un workflow de secret d’application|
|Nom de l’application pour le suivi                      |TraceAppName                              |ApplicationNameForTracing                     |Valeur de chaîne qui indique au service le nom de l’application à utiliser lors du suivi de la demande en interne|
|Jeton d’application                                 |AppToken                                  |ApplicationToken                              |Valeur de chaîne qui indique au client d’effectuer l’authentification de l’application avec le jeton de porteur spécifié|
|ID d’autorité                                      |TenantId                                  |Authority                                     |Valeur de chaîne qui fournit le nom ou l’ID du locataire dans lequel l’application est inscrite.|
|                                                  |                                          |EmbeddedManagedIdentity                       |Valeur de chaîne qui indique au client l’identité d’application à utiliser avec l’authentification d’identité gérée. Utilisez `system` pour indiquer l’identité affectée par le système. Cette propriété ne peut pas être définie avec une chaîne de connexion, par programmation uniquement.|ManagedServiceIdentity                        |TODO|
|Nom unique du sujet du certificat d’application|Objet du certificat d’application           |ApplicationCertificateSubjectDistinguishedName||
|Nom unique de l’émetteur du certificat d’application |Émetteur du certificat de l’application            |ApplicationCertificateIssuerDistinguishedName ||
|Certificat d’application Envoyer un certificat public   |Certificat d’application SendX5c, SendX5c  |ApplicationCertificateSendPublicCertificate   ||



### <a name="client-communication-properties"></a>Propriétés de la communication client

|Nom de la propriété                      |Autres noms|Nom de programmation  |Description                                                   |
|-----------------------------------|-----------------|-------------------|--------------------------------------------------------------|
|Accepter      ||Accepter      |Valeur booléenne qui demande les objets d’erreur détaillés à retourner en cas d’échec.|
|Diffusion en continu   ||Diffusion en continu   |Une valeur booléenne qui demande au client n’accumule pas les données avant de les fournir à l’appelant.|
|Non compressé||Non compressé|Une valeur booléenne qui demande au client ne demande pas de compression au niveau du transport.|

## <a name="authentication-properties-details"></a>Propriétés de l’authentification (détails)

L’une des tâches importantes de la chaîne de connexion consiste à indiquer au client comment s’authentifier auprès du service.
L’algorithme suivant est généralement utilisé par les clients pour l’authentification par rapport aux points de terminaison HTTP/HTTPs :

1. Si AadFederatedSecurity a la valeur true :
    1. Si UserToken est spécifié, utiliser l’authentification fédérée AAD avec le jeton spécifié
    1. Sinon, si ApplicationToken est spécifié, effectuer l’authentification fédérée avec le jeton spécifié
    1. Sinon, si ApplicationClientId et ApplicationKey sont spécifiés, effectuez une authentification fédérée avec l’ID client et la clé d’application spécifiés.
    1. Dans le cas contraire, si ApplicationClientId et ApplicationCertificateThumbprint sont spécifiés, effectuez une authentification fédérée avec l’ID client et le certificat spécifiés de l’application.
    1. Dans le cas contraire, effectuez une authentification fédérée avec l’identité de l’utilisateur connecté actuel (l’utilisateur sera invité à indiquer s’il s’agit de la première authentification dans la session).

1. Sinon, ne vous authentifiez pas.


### <a name="aad-federated-application-authentication-with-application-certificate"></a>Authentification des applications fédérées AAD avec un certificat d’application

1. L’authentification basée sur le certificat d’une application est prise en charge uniquement pour les applications Web (et non pour les applications clientes natives).
1. L’application Web doit être configurée pour accepter le certificat donné. [Comment authentifier le certificat de l’application AAD basée sur AAD](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)
1. L’application Web doit être configurée en tant que principal autorisé dans le cluster Kusto approprié.
1. Le certificat avec l’empreinte numérique donnée doit être installé (dans le magasin de l’ordinateur local ou dans le magasin de l’utilisateur actuel).
1. La clé publique du certificat doit contenir au moins 2048 bits.

## <a name="aad-based-authentication-examples"></a>Exemples d’authentification basés sur AAD

**Authentification fédérée AAD à l’aide de l’identité de l’utilisateur actuellement connecté (l’utilisateur sera invité si nécessaire)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;Authority Id={authority}"
```

**Authentification fédérée AAD avec l’indicateur d’ID d’utilisateur (l’utilisateur sera invité, le cas échéant)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var userUPN = "johndoe@contoso.com";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
  .WithAadUserPromptAuthentication(authority);
kustoConnectionStringBuilder.UserID = userUPN;

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    UserID = userUPN,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;User ID={userUPN};Authority Id={authority}"
```

**Authentification des applications fédérées AAD à l’aide de ApplicationClientId et ApplicationKey**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var applicationClientId = <ApplicationClientId>;
var applicationKey = <ApplicationKey>;

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationKeyAuthentication(applicationClientId, applicationKey, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    InitialCatalog = "NetDefaultDB",
    ApplicationClientId = applicationClientId,
    ApplicationKey = applicationKey,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppKey={applicationKey};Authority Id={authority}"
```

**Authentification fédérée AAD à l’aide d’un jeton d’utilisateur/d’application**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
var access_token = "<access token obtained from AAD>"

// Recommended syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadUserTokenAuthentication(access_token, authority);

// Legacy syntax - AAD User token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    UserToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: "Data Source={serviceUri};Database=NetDefaultDB;Fed=True;UserToken={access_token};Authority Id={authority}"

// Recommended syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationTokenAuthentication(access_token, authority);

// Legacy syntax - AAD Application token
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationToken = access_token,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppToken={applicationToken};Authority Id={authority}"
```

**Utilisation du rappel du fournisseur de jetons (qui sera appelé chaque fois qu’un jeton est requis)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
Func<string> tokenProviderCallback; // User-defined method to retrieve the access token

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadTokenProviderAuthentication(tokenProviderCallback);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    TokenProviderCallback = () => Task.FromResult(tokenProviderCallback()),
};
```

**Utilisation d’une identité gérée**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var managedIdentity = "<managed identity>"; // For system-assigned identity use "system"

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadManagedIdentity(managedIdentity);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    EmbeddedManagedIdentity = managedIdentity,
};
```

**Utilisation du certificat X. 509**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
X509Certificate2 applicationCertificate = "<certificate blob>";
bool sendX5c = <desired value>; // Set too 'True' to use Trusted Issuer feature of AAD

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationCertificateAuthentication(applicationClientId, applicationCertificate, authority, sendX5c);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateBlob = applicationCertificate,
    ApplicationCertificateSendX5c = sendX5c,
    Authority = authority,
};
```

**Utilisation du certificat X. 509 par empreinte numérique (le client tente de charger le certificat à partir du magasin local)**

```csharp
var serviceUri = "Service URI, typically of the form https://cluster.region.kusto.windows.net";
var authority = "contoso.com"; // Or the AAD tenant GUID: "..."
string applicationClientId = "<applicationClientId>";
string applicationCertificateThumbprint = "<ApplicationCertificateThumbprint>";

// Recommended syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
    .WithAadApplicationThumbprintAuthentication(applicationClientId, applicationCertificateThumbprint, authority);

// Legacy syntax
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(serviceUri)
{
    FederatedSecurity = true,
    ApplicationClientId = applicationClientId,
    ApplicationCertificateThumbprint = applicationCertificateThumbprint,
    Authority = authority,
};

// Equivalent Kusto connection string: $"Data Source={serviceUri};Database=NetDefaultDB;Fed=True;AppClientId={applicationClientId};AppCert={applicationCertificateThumbprint};Authority Id={authority}"
```
