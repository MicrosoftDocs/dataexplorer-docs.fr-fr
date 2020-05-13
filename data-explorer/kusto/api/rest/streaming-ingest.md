---
title: Requête HTTP d’ingestion de diffusion en continu-Azure Explorateur de données
description: Cet article décrit la requête HTTP d’ingestion de diffusion en continu dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 87b68e4a9de42a9a7085238db5919066d577ed1f
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373537"
---
# <a name="streaming-ingestion-http-request"></a>Requête HTTP d’ingestion de diffusion en continu

## <a name="request-verb-and-resource"></a>Verbe et ressource de la demande

|Action    |Verbe HTTP|Ressource HTTP                                               |
|----------|---------|------------------------------------------------------------|
|Ingestion    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>Paramètres de la demande

| Paramètre    | Description                                                                 | Obligatoire ou facultatif |
|--------------|-----------------------------------------------------------------------------|-------------------|
| `{database}` |   Nom de la base de données cible pour la demande d’ingestion                     |  Obligatoire         |
| `{table}`    |   Nom de la table cible pour la demande d’ingestion                        |  Obligatoire         |

## <a name="additional-parameters"></a>Paramètres supplémentaires

Les paramètres supplémentaires sont mis en forme en tant que paires de requêtes `{name}={value}` d’URL, séparés par le caractère &.

| Paramètre    | Description                                                                          | Obligatoire ou facultatif   |
|--------------|--------------------------------------------------------------------------------------|---------------------|
|`streamFormat`| Spécifie le format des données dans le corps de la demande. La valeur doit être l’une des suivantes : `CSV` , `TSV` , `SCsv` , `SOHsv` , `PSV` , `JSON` , `MultiJSON` , `Avro` . Pour plus d’informations, consultez [formats de données pris en charge](../../../ingestion-supported-formats.md).| Obligatoire |
|`mappingName` | Nom du mappage d’ingestion préalablement créé défini sur la table. Pour plus d’informations, consultez [mappages de données](../../management/mappings.md). La façon de gérer les mappages précréés sur la table est décrite [ici](../../management/create-ingestion-mapping-command.md).| Facultatif, mais obligatoire si `streamFormat` est un de `JSON` , `MultiJSON` ou.`Avro`|  |
              
Par exemple, pour ingérer des données au format CSV dans une table `Logs` de la base de données `Test` , utilisez :

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

Pour recevoir des données au format JSON avec un mappage créé au préalable `mylogmapping` , utilisez :

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

## <a name="request-headers"></a>En-têtes de requête

Le tableau suivant contient les en-têtes courants pour les opérations de requête et de gestion.

|En-tête standard   | Description                                                                               | Obligatoire ou facultatif | 
|------------------|-------------------------------------------------------------------------------------------|-------------------|
|`Accept`          | Définissez cette valeur sur `application/json` .                                                     | Facultatif          |
|`Accept-Encoding` | Les encodages pris en charge sont `gzip` et `deflate` .                                             | Facultatif          | 
|`Authorization`   | Consultez [authentification](./authentication.md).                                                | Obligatoire          |
|`Connection`      | Activez `Keep-Alive`.                                                                      | Facultatif          |
|`Content-Length`  | Spécifiez la longueur du corps de la demande, quand elle est connue.                                              | Facultatif          |
|`Content-Encoding`| A `gzip` la valeur, mais le corps doit être compressé avec gzip                                        | Facultatif          |
|`Expect`          | Défini sur `100-Continue`.                                                                    | Facultatif          |
|`Host`            | Définissez sur le nom de domaine auquel vous avez envoyé la demande (par exemple, `help.kusto.windows.net` ). | Obligatoire          |

Le tableau suivant contient les en-têtes personnalisés courants pour les opérations de requête et de gestion. Sauf indication contraire, les en-têtes sont destinés uniquement à des fins de télémétrie et n’ont aucun impact sur les fonctionnalités.

|En-tête personnalisé           |Description                                                                           | Obligatoire ou facultatif |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |Nom (convivial) de l’application qui effectue la demande.                            | Facultatif          |
|`x-ms-user`             |Nom (convivial) de l’utilisateur qui effectue la demande.                                   | Facultatif          |
|`x-ms-user-id`          |Identique à `x-ms-user`.                                                                  | Facultatif          |
|`x-ms-client-request-id`|Identificateur unique de la requête.                                                  | Facultatif          |
|`x-ms-client-version`   |Identificateur de version (convivial) du client qui effectue la demande. Obligatoire dans les scénarios où il est utilisé pour identifier la demande, par exemple l’annulation d’une requête en cours d’exécution.                                                        | Facultatif/obligatoire  |

## <a name="body"></a>body

Le corps correspond aux données réelles à ingérer. Les formats textuels doivent utiliser l’encodage UTF-8.

## <a name="examples"></a>Exemples

L’exemple suivant illustre la requête HTTP Après l’ingestion du contenu JSON :

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

En-têtes de requête :

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 161
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

Corps de la requête :

```txt
{"Timestamp":"2018-11-14 11:34","Level":"Info","EventText":"Nothing Happened"}
{"Timestamp":"2018-11-14 11:35","Level":"Error","EventText":"Something Happened"}
```

L’exemple suivant montre la requête HTTP HTTP pour la réception des mêmes données compressées.

```txt
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```

En-têtes de requête :

```txt
Authorization: Bearer ...AzureActiveDirectoryAccessToken...
Accept-Encoding: deflate
Accept-Encoding: gzip
Connection: Keep-Alive
Content-Length: 116
Content-Encoding: gzip
Host: help.kusto.windows.net
x-ms-client-request-id: MyApp.Ingest;5c0656b9-37c9-4e3a-a671-5f83e6843fce
x-ms-user-id: alex@contoso.com
x-ms-app: MyApp
```

Corps de la requête :

```
... binary data ...
```
