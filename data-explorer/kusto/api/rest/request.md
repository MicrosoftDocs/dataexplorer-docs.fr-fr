---
title: Demande http://query/management HTTP - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la requête/gestion DE la demande HTTP dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 71fac7122ca51755beaa09ce3867143806e4f2b3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502718"
---
# <a name="querymanagement-http-request"></a>Requête HTTP d’interrogation ou de gestion

## <a name="request-verb-and-resource"></a>Demander verbe et ressource

|Action    |Verbe HTTP|Ressource HTTP   |
|----------|---------|----------------|
|Requête     |GET      |`/v1/rest/query`|
|Requête     |POST     |`/v1/rest/query`|
|Requête v2  |GET      |`/v2/rest/query`|
|Requête v2  |POST     |`/v2/rest/query`|
|Gestion|POST     |`/v1/rest/mgmt` |

Par exemple, pour envoyer une commande de contrôle (« gestion ») à un point de terminaison de service, utilisez la ligne de demande suivante :

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

(Voir ci-dessous pour les en-têtes de demande et le corps à inclure.)

## <a name="request-headers"></a>En-têtes de requête

Le tableau suivant contient les en-têtes communs utilisés pour effectuer des opérations de requête et de gestion.

|En-tête standard  |Description                                                                                                                    |
|-----------------|-------------------------------------------------------------------------------------------------------------------------------|
|`Accept`         |**Obligatoire**. Définissez cette propriété sur `application/json`.                                                                                  |
|`Accept-Encoding`|**Facultatif**. Les codages `gzip` `deflate`pris en charge sont et .                                                                    |
|`Authorization`  |**Obligatoire**. Voir [l’authentification](./authentication.md).                                                                       |
|`Connection`     |**Facultatif**. Il est `Keep-Alive` recommandé d’être activé.                                                                  |
|`Content-Length` |**Facultatif**. Il est recommandé que la longueur du corps de demande soit spécifiée lorsqu’elle est connue.                                          |
|`Content-Type`   |**Obligatoire**. Réglez `application/json` `charset=utf-8`ceci à avec .                                                             |
|`Expect`         |**Facultatif**. Peut être `100-Continue`réglé à .                                                                                    |
|`Host`           |**Obligatoire**. Réglez ceci au nom de domaine entièrement qualifié à `help.kusto.windows.net`qui la demande a été envoyée (par exemple, ).|

Le tableau suivant contient les en-têtes personnalisés communs utilisés lors de l’exécution des opérations de requête et de gestion. Sauf indication contraire, ces en-têtes sont utilisés uniquement à des fins de télémétrie et n’ont aucun impact de fonctionnalité.

Tous les en-têtes sont **facultatifs.** Il est **fortement recommandé** que `x-ms-client-request-id` vous spécifiiez l’en-tête personnalisé. Dans certains scénarios (par exemple, l’annulation d’une requête en cours d’exécution) cet en-tête est **obligatoire** parce qu’il est utilisé pour identifier la demande.


|En-tête personnalisé           |Description                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |Le nom (amical) de la demande faisant la demande.                                                |
|`x-ms-user`             |Le nom (amical) de l’utilisateur faisant la demande.                                                       |
|`x-ms-user-id`          |Identique à `x-ms-user`.                                                                                      |
|`x-ms-client-request-id`|Identificateur unique de la requête.                                                                      |
|`x-ms-client-version`   |L’identifiant de version (amicale) pour le client qui fait la demande.                                      |
|`x-ms-readonly`         |Si elle est spécifiée, oblige la demande à fonctionner en mode lecture uniquement (l’empêchant d’apporter des modifications durables).|

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres suivants peuvent être adoptés dans la demande. Ils sont codés dans la demande comme paramètres de requête ou dans le cadre du corps, selon que GET ou POST est utilisé.

* `csl`: Il s’agit d’un paramètre **obligatoire.** Il contient le texte de la requête ou de la commande de contrôle à exécuter.

* `db`: Il s’agit d’un paramètre **facultatif** pour certaines commandes de contrôle, et un paramètre **obligatoire** pour d’autres commandes de contrôle et toutes les requêtes. Il fournit le nom de la "base de données dans la portée" - la base de données qui est la cible de la requête ou la commande de contrôle.

* `properties`: Il s’agit d’un paramètre **facultatif.** Il fournit diverses propriétés de demande de client qui modifient la façon dont la demande est traitée et ses résultats. Pour une description complète, voir [les propriétés de demande du client](../netfx/request-properties.md).

## <a name="get-query-parameters"></a>Paramètres de requête GET

Lorsque GET est utilisé, les paramètres de requête de la demande spécifient les paramètres de demande mentionnés ci-dessus.

## <a name="body"></a>body

Lorsque post est utilisé, le corps de la demande est un seul document JSON codé dans UTF-8 qui fournit les valeurs des paramètres de demande mentionnés ci-dessus.

## <a name="examples"></a>Exemples

L’exemple suivant montre la demande de requête HTTP POST :

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

En-têtes de requête :

```txt
Accept: application/json
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Content-Type: application/json; charset=utf-8
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1
x-ms-user-id: EARTH\davidbg
x-ms-app: MyApp
```

Organisme de demande (nouvelles lignes introduites pour la clarté; elles ne sont pas nécessaires):

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

L’exemple suivant montre comment créer une demande qui envoie la requête ci-dessus en utilisant [curl](https://curl.haxx.se/):

1. Obtenir un jeton pour l’authentification.

* `AAD_TENANT_NAME_OR_ID`remplacer, `AAD_APPLICATION_ID` `AAD_APPLICATION_KEY` et par les valeurs pertinentes, après avoir mis en place [l’authentification de l’application AAD](../../management/access-control/how-to-provision-aad-app.md)

```
curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
  -F "grant_type=client_credentials" \
  -F "resource=https://help.kusto.windows.net" \
  -F "client_id=AAD_APPLICATION_ID" \
  -F "client_secret=AAD_APPLICATION_KEY"
```

Cela vous fournira le jeton porteur:

```
{
  "token_type": "Bearer",
  "expires_in": "3599",
  "ext_expires_in":"3599", 
  "expires_on":"1578439805",
  "not_before":"1578435905",
  "resource":"https://help.kusto.windows.net",
  "access_token":"eyJ0...uXOQ"
}
```

2. Utilisez le jeton porteur dans votre demande au point de terminaison de la requête :

```
curl -d '{"db":"Samples","csl":"print Test=\"Hello, World!\"","properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"}}"}"' \
-H "Accept: application/json" \
-H "Authorization: Bearer eyJ0...uXOQ" \
-H "Content-Type: application/json; charset=utf-8" \
-H "Host: help.kusto.windows.net" \
-H "x-ms-client-request-id: MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1" \
-H "x-ms-user-id: EARTH\davidbg" \
-H "x-ms-app: MyApp" \
-X POST https://help.kusto.windows.net/v2/rest/query
```

3. Lire la réponse selon [cette spécification](response.md).