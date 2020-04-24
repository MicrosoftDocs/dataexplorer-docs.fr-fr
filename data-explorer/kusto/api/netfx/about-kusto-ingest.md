---
title: Kusto Ingest Client Library - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto Ingest Client Library dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0fd86579f4ca6471903305ca9249b78bc03b8cdf
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503126"
---
# <a name="kusto-ingest-client-library"></a>Bibliothèque cliente Kusto Ingest

## <a name="overview"></a>Vue d’ensemble
La bibliothèque Kusto.Ingest est une bibliothèque .NET 4.6.2 qui permet d’envoyer des données au service Kusto.
Kusto.Ingest prend des dépendances sur les bibliothèques suivantes et SDKs:

* ADAL pour AAD Authentification
* Client de stockage Azure
* [TBD : liste complète des dépendances externes]

Les méthodes d’ingestion de Kusto sont définies par l’interface [IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient) et permettent l’ingestion de données de Stream, IDataReader, fichier local(s), et Azure blob(s) en mode synchrone et asynchrone.

## <a name="ingest-client-flavors"></a>Saveurs ingest client
Conceptuellement, il y a deux saveurs de base du client Ingest : File d’attente et direct.

### <a name="queued-ingestion"></a>Ingestion en file d’attente
Défini par [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), ce mode limite la dépendance au code client vis-à-vis du service Kusto. L’ingestion est effectuée en affichant un message d’ingestion Kusto à une file d’attente Azure, qui est ensuite acquise auprès du service Kusto Data Management (Ingestion). Tous les artefacts de stockage intermédiaires seront créés par le client ingérer à l’aide des ressources allouées par le service de gestion des données Kusto.

**Les avantages du mode file d’attente** comprennent (mais ne se limitent pas à):

* Découplage du processus d’ingestion de données du service Kusto Engine
* Permet de persister les demandes d’ingestion lorsque le service Kusto Engine (ou Ingestion) n’est pas disponible
* Permet une agrégation efficace et contrôlable des données entrantes par le service Ingestion, améliorant ainsi les performances
* Permet au service d’ingestion Kusto de gérer la charge d’ingestion sur le service Kusto Engine
* Le service d’ingestion Kusto sera retenti au besoin sur les défaillances d’ingestion transitoires (par exemple, la limitation XStore)
* Fournit un mécanisme pratique pour suivre l’avancement et le résultat de chaque demande d’ingestion

Le diagramme suivant décrit l’interaction avec kusto :

![texte de remplacement](../images/queued-ingest.jpg "file d’attente-ingest")

### <a name="direct-ingestion"></a>Ingestion directe
Défini par IKustoDirectIngestClient, ce mode force une interaction directe avec le service Kusto Engine. Dans ce mode, le service d’ingestion Kusto ne modère ni ne gère les données. Chaque demande d’ingestion en mode `.ingest` Direct est finalement traduite dans la commande exécutée directement sur le service Kusto Engine.
Le diagramme suivant décrit l’interaction directe avec kusto :

![texte de remplacement](../images/direct-ingest.jpg "direct-ingest")

> [!NOTE]
> Le mode Direct n’est pas recommandé pour les solutions d’ingestion de qualité de production.

**Les avantages du mode Direct** comprennent :

* Faible latence (il n’y a pas d’agrégation). Cependant, une faible latence peut également être réalisée avec l’ingestion
* Lorsque des méthodes synchrones sont utilisées, l’achèvement de la méthode indique la fin de l’opération d’ingestion

**Les inconvénients du mode Direct** comprennent :

* Le code client doit implémenter toute logique de nouvelle tentative ou de traitement des erreurs
* Les ingestions sont impossibles lorsque le service Kusto Engine n’est pas disponible
* Le code client pourrait submerger le service Kusto Engine avec des demandes d’ingestion, car il n’est pas au courant de la capacité de service Moteur

## <a name="ingestion-best-practices"></a>Ingestion Des meilleures pratiques

### <a name="general"></a>Général
[Les meilleures pratiques d’ingestion](kusto-ingest-best-practices.md) fournissent des COG et des POV de débit sur l’ingestion.

### <a name="thread-safety"></a>Sécurité des threads
Les implémentations de Kusto Ingest Client sont sans fil et sont destinées à être réutilisées. Il n’est pas nécessaire `KustoQueuedIngestClient` de créer un exemple de classe pour chacune ou même plusieurs opérations d’iningrage. Une seule `KustoQueuedIngestClient` instance de est nécessaire par cible Kusto cluster par processus utilisateur. L’exécution de plusieurs instances est contre-productive et peut doS le cluster de gestion des données.

### <a name="supported-data-formats"></a>Formats de données pris en charge
Lorsque vous utilisez l’ingestion indigène, si elle n’est pas déjà là, téléchargez les données sur un ou plusieurs blobs de stockage Azure. Les formats blob actuellement pris en charge sont documentés dans le sujet [des formats de données pris en](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) charge.

### <a name="schema-mapping"></a>Mappage de schéma
[Les cartographies de schéma aident](../../management/mappings.md) avec des champs de données source déterministiquement contraignants aux colonnes de table de destination.

## <a name="usage-and-further-reading"></a>Utilisation et lecture ultérieure

* Comme décrit ci-dessus, la base recommandée pour des solutions d’ingestion durables et à grande échelle pour Kusto devrait être le **KustoQueuedIngestClient**.
* Pour minimiser la charge inutile sur votre service Kusto, il est recommandé qu’une seule instance du client Kusto Ingest (Fileur ou Direct) soit utilisée par processus par cluster Kusto. La mise en œuvre du client Kusto Ingest est sans danger et entièrement réentrante.

### <a name="ingestion-permissions"></a>Autorisations d’ingestion
* [Kusto Ingestion Permissions](kusto-ingest-client-permissions.md) explique la configuration des autorisations requises pour une ingestion réussie à l’aide du paquet Kusto.Ingest

### <a name="kustoingest-library-reference"></a>Référence de la bibliothèque Kusto.Ingest
* [Kusto.Ingest Client Reference](kusto-ingest-client-reference.md) contient une référence complète des interfaces et implémentations des clients Kusto.<BR>Vous y trouverez l’information sur la façon de créer des clients ingérer, d’augmenter les demandes d’ingestion, de gérer les progrès de l’ingestion et plus encore
* [Kusto.Ingest Operation Status](kusto-ingest-client-status.md) explique les installations **de KustoQueuedIngestClient** pour le suivi de l’état d’ingestion
* [Kusto.Ingest Errors documente](kusto-ingest-client-errors.md) kusto Ingérer les erreurs et les exceptions des clients
* [Kusto.Ingest Exemples](kusto-ingest-client-examples.md) présente des extraits de code démontrant diverses techniques d’ingestion de données dans Kusto

### <a name="data-ingestion-rest-apis"></a>Ingestion de données REST API
[Data Ingestion without Kusto.Ingest Library](kusto-ingest-client-rest.md) explique comment implémenter l’ingestion De Kusto en utilisant les API Kusto REST et sans dépendre de la bibliothèque Kusto.Ingest.

