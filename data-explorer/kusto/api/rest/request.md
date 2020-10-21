---
title: Requête HTTP de requête/gestion-Azure Explorateur de données
description: Cet article décrit la requête HTTP Query/Management dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: cf9f9e5f6a9c5afca58e2637ed4e639882e3749d
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337412"
---
# <a name="querymanagement-http-request"></a>Requête HTTP d’interrogation ou de gestion

## <a name="request-verb-and-resource"></a>Verbe et ressource de la demande

|Action    |Verbe HTTP|Ressource HTTP   |
|----------|---------|----------------|
|Requête     |GET      |`/v1/rest/query`|
|Requête     |POST     |`/v1/rest/query`|
|Requête v2  |GET      |`/v2/rest/query`|
|Requête v2  |POST     |`/v2/rest/query`|
|Gestion|POST     |`/v1/rest/mgmt` |

Par exemple, pour envoyer une commande de contrôle (« gestion ») à un point de terminaison de service, utilisez la ligne de requête suivante :

```
POST https://help.kusto.windows.net/v1/rest/mgmt HTTP/1.1
```

Consultez ci-dessous pour connaître les en-têtes et le corps de la requête à inclure.

## <a name="request-headers"></a>En-têtes de requête

Le tableau suivant contient les en-têtes courants utilisés pour les opérations de requête et de gestion.

|En-tête standard  |Description                                                                                 |Obligatoire ou facultatif |
|-----------------|--------------------------------------------------------------------------------------------|------------------|
|`Accept`         |Paramètre à définir sur `application/json`                                                                   |Obligatoire          |
|`Accept-Encoding`|Les encodages pris en charge sont `gzip` et `deflate`                                                |Facultatif          |
|`Authorization`  |Voir [authentification](./authentication.md)                                                   |Obligatoire          |
|`Connection`     |Nous vous recommandons d’activer `Keep-Alive`                                                   |Facultatif          |
|`Content-Length` |Nous vous recommandons de spécifier la longueur du corps de la demande quand elle est connue.                            |Facultatif          |
|`Content-Type`   |Affecter à la valeur `application/json` avec `charset=utf-8`                                              |Obligatoire          |
|`Expect`         |Peut avoir la valeur `100-Continue`                                                                |Facultatif          |
|`Host`           |Défini sur le nom de domaine complet auquel la demande a été envoyée (par exemple, `help.kusto.windows.net` ) |Obligatoire|

Le tableau suivant contient les en-têtes personnalisés communs utilisés pour les opérations de requête et de gestion. Sauf indication contraire, ces en-têtes sont utilisés à des fins de télémétrie uniquement et n’ont aucun impact sur les fonctionnalités.

Tous les en-têtes sont facultatifs. Nous vous recommandons de spécifier l' `x-ms-client-request-id` en-tête personnalisé. Dans certains scénarios, comme l’annulation d’une requête en cours d’exécution, cet en-tête est requis, car il est utilisé pour identifier la demande.

|En-tête personnalisé           |Description                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |Nom (convivial) de l’application à l’origine de la demande                                                 |
|`x-ms-user`             |Nom (convivial) de l’utilisateur qui effectue la demande                                                        |
|`x-ms-user-id`          |Identique à `x-ms-user`                                                                                       |
|`x-ms-client-request-id`|Identificateur unique de la demande                                                                       |
|`x-ms-client-version`   |Identificateur de version (convivial) du client qui effectue la requête                                       |
|`x-ms-readonly`         |S’il est spécifié, force l’exécution de la requête en lecture seule, en l’empêchant d’effectuer des modifications durables. |

## <a name="request-parameters"></a>Paramètres de la demande

Les paramètres suivants peuvent être passés dans la demande. Ils sont encodés dans la requête en tant que paramètres de requête ou dans le cadre du corps, selon que la fonction d’extraction ou de publication est utilisée.

|Paramètre   |Description                                                                                 |Obligatoire ou facultatif |
|------------|--------------------------------------------------------------------------------------------|------------------|
|`csl`       |Texte de la commande de requête ou de contrôle à exécuter                                             |Obligatoire          |
|`db`        |Nom de la base de données dans l’étendue qui est la cible de la commande de requête ou de contrôle            |Facultatif pour certaines commandes de contrôle. <br>Obligatoire pour d’autres commandes et toutes les requêtes. </br>                                                                   |
|`properties`|Fournit des propriétés de demande client qui modifient le mode de traitement de la demande et ses résultats. Pour plus d’informations, consultez [Propriétés des demandes clientes](../netfx/request-properties.md) .                                               | Facultatif         |

## <a name="get-query-parameters"></a>OBTENIR les paramètres de requête

Quand la requête est utilisée, les paramètres de requête de la demande spécifient les paramètres de la requête.

## <a name="body"></a>Corps

Lorsque la publication est utilisée, le corps de la requête est un document JSON unique encodé au format UTF-8, avec les valeurs des paramètres de la demande.

## <a name="examples"></a>Exemples

Cet exemple montre la requête HTTP après pour une requête.

```txt
POST https://help.kusto.windows.net/v2/rest/query HTTP/1.1
```

En-têtes de requête

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

Corps de la demande

```json
{
  "db":"Samples",
  "csl":"print Test=\"Hello, World!\"",
  "properties":"{\"Options\":{\"queryconsistency\":\"strongconsistency\"},\"Parameters\":{},\"ClientRequestId\":\"MyApp.Query;e9f884e4-90f0-404a-8e8b-01d883023bf1\"}"
}
```

Cet exemple montre comment créer une requête qui envoie la requête ci-dessus à l’aide de la [boucle](https://curl.haxx.se/).

1. Obtenez un jeton pour l’authentification.

    Remplacez `AAD_TENANT_NAME_OR_ID` , `AAD_APPLICATION_ID` et `AAD_APPLICATION_KEY` par les valeurs pertinentes, après avoir configuré [l’authentification de l’application AAD](../../../provision-azure-ad-app.md)

    ```
    curl "https://login.microsoftonline.com/AAD_TENANT_NAME_OR_ID/oauth2/token" \
      -F "grant_type=client_credentials" \
      -F "resource=https://help.kusto.windows.net" \
      -F "client_id=AAD_APPLICATION_ID" \
      -F "client_secret=AAD_APPLICATION_KEY"
    ```

    Cet extrait de code vous fournira le jeton du porteur.

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

1. Utilisez le jeton du porteur dans votre demande au point de terminaison de la requête.

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

1. Lire la réponse en fonction de [cette spécification](response.md).