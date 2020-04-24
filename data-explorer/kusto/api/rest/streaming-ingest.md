---
title: Diffusion en continu DE la demande HTTP - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la demande d’ingestion en continu HTTP dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d14987806bbc62dbc79112700bd5b88aaa6676c3
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503007"
---
# <a name="streaming-ingestion-http-request"></a>Diffusion en continu DE la demande HTTP

## <a name="request-verb-and-resource"></a>Demander verbe et ressource

|Action    |Verbe HTTP|Ressource HTTP                                               |
|----------|---------|------------------------------------------------------------|
|Ingestion    |POST     |`/v1/rest/ingest/{database}/{table}?{additional parameters}`|

## <a name="request-parameters"></a>Paramètres de la demande

| Paramètre    |  Description                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
| `{database}` | **Nécessaire** Nom de la base de données cible pour la demande d’ingestion                                          |
| `{table}`    | **Nécessaire** Nom du tableau cible pour la demande d’ingestion                                             |

## <a name="additional-parameters"></a>Paramètres supplémentaires
D’autres paramters sont formatés `{name}` = `{value}` sous forme de requête URL : paires séparées par & caractère


| Paramètre    |  Description                                                                                                |
|--------------|-------------------------------------------------------------------------------------------------------------|
|`streamFormat`| **Nécessaire** Spécifie le format des données dans l’organe de demande. La valeur devrait être l’une`SOHsv``Psv`des`Json``SingleJson`suivantes:`Avro` `Csv`,`Tsv`,`Scsv`, , , , ,`MultiJson`, . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . . Pour plus d’informations, veuillez consulter [les formats de données pris en charge](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats).|
|`mappingName` | **Nécessaire** `streamFormat` si est `Json` `SingleJson`l’un de , , `MultiJson` ou `Avro`, **Facultatif** autrement. La valeur doit être le nom de la cartographie d’ingestion pré-créée définie sur la table. Pour plus d’informations sur les cartographies de données, consultez [data Mappings](../../management/mappings.md). La façon de gérer les cartes précréttives sur la table est décrite [ici](../../management/create-ingestion-mapping-command.md) |
              

Par exemple, pour inondérer les données `Logs` formatées par CSV en table dans la base de données, `Test` utilisez la ligne de demande suivante :

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Csv HTTP/1.1
```

d’ingérer des données formatées par JSON avec une cartographie précrétée`mylogmapping`

```
POST https://help.kusto.windows.net/v1/rest/ingest/Test/Logs?streamFormat=Json&mappingName=mylogmapping HTTP/1.1
```


(Voir ci-dessous pour les en-têtes de demande et le corps à inclure.)

## <a name="request-headers"></a>En-têtes de requête

Le tableau suivant contient les en-têtes communs utilisés pour effectuer des opérations de requête et de gestion.

|En-tête standard  |Description                                                                                                              |
|------------------|------------------------------------------------------------------------------------------------------------------------|
|`Accept`          |**Facultatif**. Définissez cette propriété sur `application/json`.                                                                           |
|`Accept-Encoding` |**Facultatif**. Les codages `gzip` `deflate`pris en charge sont et .                                                             |
|`Authorization`   |**Obligatoire**. Voir [l’authentification](./authentication.md).                                                                |
|`Connection`      |**Facultatif**. Il est `Keep-Alive` recommandé d’être activé.                                                           |
|`Content-Length`  |**Facultatif**. Il est recommandé que la longueur du corps de demande soit spécifiée lorsqu’elle est connue.                                   |
|`Content-Encoding`|**Facultatif**. Peut être `gzip` réglé à quel cas le corps est nécessaire pour être gzip-compressé                                 |
|`Expect`          |**Facultatif**. Peut être `100-Continue`réglé à .                                                                             |
|`Host`            |**Obligatoire**. Définissez ceci au nom de domaine entièrement qualifié à qui `help.kusto.windows.net`la demande a été envoyée (p. ex., ).|

Le tableau suivant contient les en-têtes personnalisés communs utilisés lors de l’exécution des opérations de requête et de gestion. Sauf indication contraire, ces en-têtes sont utilisés uniquement à des fins de télémétrie et n’ont aucun impact de fonctionnalité.

Tous les en-têtes sont **facultatifs.** Il est **toutefois fortement** `x-ms-client-request-id` recommandé que l’en-tête personnalisé soit spécifié. Dans certains scénarios (p. ex., l’annulation d’une requête en cours d’exécution) cet en-tête est **obligatoire** car il est utilisé pour identifier la demande.


|En-tête personnalisé           |Description                                                                                               |
|------------------------|----------------------------------------------------------------------------------------------------------|
|`x-ms-app`              |Le nom (amical) de la demande faisant la demande.                                                |
|`x-ms-user`             |Le nom (amical) de l’utilisateur faisant la demande.                                                       |
|`x-ms-user-id`          |Identique à `x-ms-user`.                                                                                      |
|`x-ms-client-request-id`|Identificateur unique de la requête.                                                                      |
|`x-ms-client-version`   |L’identifiant de version (amicale) pour le client qui fait la demande.                                      |

## <a name="body"></a>body

Le corps est les données réelles à ingérer. Les formats textuels shoud utilisent l’encodage UTF-8.

## <a name="examples"></a>Exemples

L’exemple suivant montre la demande HTTP POST pour un contenu JSON ingérant :

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

L’exemple suivant montre la demande DE HTTP POST pour ingérer les mêmes données compressées

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