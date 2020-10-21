---
title: Liens détaillés de l’interface utilisateur-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les liens détaillés de l’interface utilisateur dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 412e489365daabfdde7de8cd61e398100f4bc3ef
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92337395"
---
# <a name="ui-deep-links"></a>Liens détaillés de l’interface utilisateur

L’API REST fournit une fonctionnalité de lien profond qui permet `GET` aux requêtes http de rediriger l’appelant vers un outil d’interface utilisateur. Par exemple, vous pouvez créer un URI qui ouvre l’outil Kusto. Explorer, le configure automatiquement pour un cluster et une base de données spécifiques, exécute une requête spécifique et affiche ses résultats à l’utilisateur.

API REST des liens profond de l’interface utilisateur :

* Cluster (obligatoire) est généralement défini implicitement, comme le service qui implémente l’API REST, mais peut être substitué en spécifiant le paramètre de requête URI `uri` .

* La base de données (facultatif) est spécifiée comme étant le premier et le seul fragment du chemin d’accès de l’URI. La base de données est obligatoire pour les requêtes et facultative pour les commandes de contrôle.

* La commande de requête ou de contrôle (facultatif) est spécifiée à l’aide du paramètre de requête URI `query` ou du paramètre de requête URI `querysrc` (qui pointe vers une ressource Web qui contient la requête).
  `query` peut être utilisé dans le texte de la commande de requête ou de contrôle proprement dite (encodé à l’aide de l’encodage de paramètre de requête HTTP). Elle peut également être utilisée dans l’encodage Base64 de l’gzip du texte de la commande de requête ou de contrôle (ce qui permet de compresser des requêtes longues afin qu’elles correspondent aux limites de longueur de l’URI du navigateur par défaut).

* Le nom de la connexion au cluster (facultatif) est spécifié à l’aide du paramètre de requête URI `name` .

* L’outil d’interface utilisateur est spécifié à l’aide du `web` paramètre de requête URI facultatif.
  `web=0` indique l’application de bureau Kusto. Explorer. `web=1` indique l’application Web Kusto. webexplorer.
  `web=2` est l’ancienne version de Kusto. webexplorer (basée sur Application Insights Analytics). `web=3` Kusto. webexplorer est-il associé à un profil vide (aucun onglet ou clusters précédemment ouverts n’est disponible). Enfin, le `web` paramètre de requête peut être remplacé par `saw=1` pour indiquer la version Saw de Kusto. Explorer.

Voici quelques exemples de liens :

* `https://help.kusto.windows.net/`: Lorsqu’un agent utilisateur (tel qu’un navigateur) émet une `GET /` requête, il est redirigé vers l’outil d’interface utilisateur par défaut configuré pour interroger le `help` cluster.
* `https://help.kusto.windows.net/Samples`: Lorsqu’un agent utilisateur (tel qu’un navigateur) émet une `GET /Samples` requête, il est redirigé vers l’outil d’interface utilisateur par défaut configuré pour interroger la `help` `Samples` base de données du cluster.
* `http://help.kusto.windows.net/Samples?query=StormEvents`: Lorsqu’un utilisateur (par exemple, un navigateur) émet une `GET /Samples?query=StormEvents` requête, il est redirigé vers l’outil d’interface utilisateur par défaut configuré pour interroger la `help` `Samples` base de données du cluster et émettre la `StormEvents` requête.

> [!NOTE]
> Les URI de lien profond ne nécessitent pas d’authentification, car l’authentification est effectuée par l’outil d’interface utilisateur utilisé pour la redirection.
> Tout `Authorization` en-tête http, s’il est fourni, est ignoré.

> [!IMPORTANT]
> Pour des raisons de sécurité, les outils d’interface utilisateur n’exécutent pas automatiquement les commandes de contrôle, même si `query` ou `querysrc` sont spécifiés dans le lien profond.

## <a name="deep-linking-to-kustoexplorer"></a>Liaison profonde à Kusto. Explorer

Cette API REST effectue une redirection qui installe et exécute l’outil client de bureau Kusto. Explorer avec des paramètres de démarrage spécialement créés qui ouvrent une connexion à un cluster de moteur Kusto spécifique et exécutent une requête sur ce cluster.

* Chemin d’accès : `/` [*DatabaseName*']
* DoVerb `GET`
* Chaîne de requête : `web=0`

> [!NOTE]
> Pour obtenir une description de la syntaxe d’URI de redirection pour le démarrage de Kusto. Explorer, consultez [Deep-linking with Kusto. Explorer](../../tools/kusto-explorer-using.md#deep-linking-queries) .

## <a name="deep-linking-to-kustowebexplorer"></a>Liaison profonde à Kusto. webexplorer

Cette API REST effectue une redirection vers Kusto. webexplorer, une application Web.

* Chemin d’accès : `/` [*DatabaseName*']
* DoVerb `GET`
* Chaîne de requête : `web=1`

## <a name="specifying-the-query-or-control-command-in-the-uri"></a>Spécification de la commande de requête ou de contrôle dans l’URI

Lorsque le paramètre de chaîne de requête URI `query` est spécifié, il doit être encodé en fonction de la chaîne de requête URI qui encodage des règles html. Le texte de la commande de requête ou de contrôle peut également être compressé par gzip, puis encodé via l’encodage Base64. Cela vous permet d’envoyer des requêtes ou des commandes de contrôle plus longues (puisque la dernière méthode d’encodage produit des URI plus courts).

## <a name="specifying-the-query-or-control-command-by-indirection"></a>Spécification de la commande de requête ou de contrôle par indirection

Si la commande de la requête ou du contrôle est très longue, même son encodage à l’aide de gzip/base64 dépassera la longueur maximale de l’URI de l’agent utilisateur. Le paramètre de chaîne de requête d’URI `querysrc` est également fourni, et sa valeur est un URI bref pointant vers une ressource Web qui contient le texte de la commande de la requête ou du contrôle.

Par exemple, il peut s’agir de l’URI d’un fichier hébergé par le stockage d’objets BLOB Azure.

> [!NOTE]
> Si le lien profond est vers l’outil d’interface utilisateur d’application Web, le service Web qui fournit la commande de requête ou de contrôle (autrement dit, le service qui fournit l' `querysrc` URI) doit être configuré pour prendre en charge cors pour `dataexplorer.azure.com` .
>
> En outre, si des informations d’authentification/d’autorisation sont requises par ce service, elles doivent être fournies dans le cadre de l’URI lui-même.
>
> Par exemple, si `querysrc` pointe vers un objet BLOB dans le stockage d’objets BLOB Azure, vous devez configurer le compte de stockage pour qu’il prenne en charge cors, puis rendre l’objet BLOB proprement public (afin qu’il puisse être téléchargé sans revendications de sécurité) ou ajouter une SAP de stockage Azure appropriée à l’URI. La configuration CORS peut être effectuée à partir de la [portail Azure](https://portal.azure.com/) ou à partir d' [Explorateur stockage Azure](https://azure.microsoft.com/features/storage-explorer/).
> Consultez [prise en charge de cors dans Azure Storage](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services).