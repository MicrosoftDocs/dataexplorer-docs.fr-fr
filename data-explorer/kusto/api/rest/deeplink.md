---
title: UI liens profonds - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les liens profonds de l’interface utilisateur dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: c9535ced274ccdbe38f8d9a765e98d11e7b381e5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523679"
---
# <a name="ui-deep-links"></a>Interface utilisateur liens profonds

L’API REST fournit une fonctionnalité `GET` de lien profond qui permet aux demandes HTTP de rediriger l’appelant vers un outil d’interface utilisateur. Par exemple, vous pouvez concevoir un URI qui ouvre l’outil Kusto.Explorer, le configure automatiquement pour un cluster et une base de données spécifiques, exécute une requête spécifique et affiche ses résultats à l’utilisateur.

L’interface utilisateur liens profonds REST API:

* Cluster (obligatoire) est généralement défini implicitement, comme le service qui met en œuvre l’API `uri`REST, mais peut être remplacé en spécifiant le paramètre de requête URI .

* La base de données (facultative) est spécifiée comme le premier et le seul fragment du chemin URI. La base de données est obligatoire pour les requêtes et facultative pour les commandes de contrôle.

* La requête ou la commande de contrôle (facultatif) `query`est spécifiée en `querysrc` utilisant le paramètre de requête URI, ou le paramètre de requête URI (qui pointe vers une ressource Web qui détient la requête).
  `query`peut être utilisé dans le texte de la requête ou de la commande de contrôle elle-même (codée à l’aide du paramètre de requête HTTP codant). Alternativement, il peut être utilisé dans l’encodage de base64 du gzip de la requête ou du texte de commande de contrôle (ce qui permet de compresser les longues requêtes afin qu’elles correspondent aux limites de longueur du navigateur par défaut URI).

* Le nom de la connexion cluster (facultatif) est `name`spécifié à l’aide du paramètre de requête URI .

* L’outil d’interface utilisateur `web` est spécifié à l’aide du paramètre de requête URI en option.
  `web=0`indique l’application de bureau Kusto.Explorer. `web=1`indique l’application Web Kusto.WebExplorer.
  `web=2`est l’ancienne version de Kusto.WebExplorer (basée dans Application Insights Analytics). `web=3`est le Kusto.WebExplorer avec un profil vide (aucun onglet ou clusters précédemment ouvert ne sera disponible). Enfin, `web` le paramètre de `saw=1` requête peut être remplacé par afin d’indiquer la version SAW de Kusto.Explorer.

Voici quelques exemples de liens :

* `https://help.kusto.windows.net/`: Lorsqu’un agent utilisateur (comme `GET /` un navigateur) émet une demande, il sera redirigé `help` vers l’outil d’interface utilisateur par défaut configuré pour interroger le cluster.
* `https://help.kusto.windows.net/Samples`: Lorsqu’un agent utilisateur (comme `GET /Samples` un navigateur) émet une demande, il sera redirigé `help` `Samples` vers l’outil d’interface utilisateur par défaut configuré pour interroger la base de données du cluster.
* `http://help.kusto.windows.net/Samples?query=StormEvents`: Lorsqu’un utilisateur (comme un `GET /Samples?query=StormEvents` navigateur) émet une demande, il sera redirigé `help` vers `Samples` l’outil `StormEvents` d’interface utilisateur par défaut configuré pour interroger la base de données du cluster et émettre la requête.

> [!NOTE]
> Les URL de lien profond ne nécessitent pas d’authentification puisque l’authentification est effectuée par l’outil d’interface utilisateur utilisé pour la redirection.
> Tout `Authorization` en-tête HTTP, s’il est fourni, est ignoré.

> [!IMPORTANT]
> Pour des raisons de sécurité, les outils d’interface utilisateur n’exécutent pas automatiquement les commandes de contrôle, même si `query` le lien profond est spécifié ou `querysrc` est spécifié.

## <a name="deep-linking-to-kustoexplorer"></a>Lien profond vers Kusto.Explorer

Cette API REST effectue la redirection qui installe et exécute l’outil client de bureau Kusto.Explorer avec des paramètres de démarrage spécialement conçus qui ouvrent une connexion à un cluster moteur Kusto spécifique et exécutent une requête contre ce cluster.

* Chemin: `/` [*DatabaseName*']
* Verbe:`GET`
* Chaîne de requête:`web=0`

> [!NOTE]
> Voir [Deep-linking avec Kusto.Explorer](../../tools/kusto-explorer.md#deep-linking-queries) pour une description de la syntaxe URI redirection pour le démarrage Kusto.Explorer.

## <a name="deep-linking-to-kustowebexplorer"></a>Lien profond vers Kusto.WebExplorer

Cette API REST effectue la redirection vers Kusto.WebExplorer, une application web.

* Chemin: `/` [*DatabaseName*']
* Verbe:`GET`
* Chaîne de requête:`web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>Spécifier la requête ou le commandement de contrôle dans l’URI

Lorsque le paramètre `query` de chaîne de requête URI est spécifié, il doit être codé selon les règles HTML codantes des chaînes de requête URI. Alternativement, le texte de la requête ou de la commande de contrôle peut être comprimé par gzip, puis codé par base64 codage. Cela vous permet d’envoyer des requêtes plus longues ou des commandes de contrôle (puisque cette dernière méthode d’encodage entraîne des URI plus courts).

## <a name="specifying-the-query-or-control-command-by-indirection"></a>Spécifier la requête ou le commandement de contrôle par indirection

Si la requête ou la commande de contrôle est très longue, même encodage à l’aide de gzip/base64 dépassera la longueur maximale URI de l’agent utilisateur. Alternativement, le paramètre `querysrc` de chaîne de requête URI est fourni, et sa valeur est une courte URI pointant vers une ressource Web qui détient la requête ou le texte de commande de contrôle.

Par exemple, cela peut être l’URI pour un fichier hébergé par Azure Blob Storage.

> [!NOTE]
> Si le lien profond est à l’outil d’interface utilisateur d’application Web, le `querysrc` service Web fournissant la requête ou `dataexplorer.azure.com`la commande de contrôle (c’est-à-dire le service fournissant l’URI) doit être configuré pour prendre en charge CORS pour .
>
> En outre, si les informations d’authentification/autorisation sont requises par ce service, elles doivent être fournies dans le cadre de l’URI elle-même.
>
> Par exemple, `querysrc` si les points à un blob dans Azure Blob Storage, il faut configurer le compte de stockage pour prendre en charge CORS, et soit rendre le blob lui-même public (de sorte qu’il peut être téléchargé sans réclamations de sécurité) ou ajouter un bon Stockage Azure SAS à l’URI. La configuration CORS peut être effectuée à partir du [portail Azure](https://portal.azure.com/) ou à partir d’Azure [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
> Voir [le support CORS dans Azure Storage](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).

