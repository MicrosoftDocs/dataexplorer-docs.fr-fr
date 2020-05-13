---
title: Bibliothèque cliente de réception Kusto-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la bibliothèque cliente de réception Kusto dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/24/2020
ms.openlocfilehash: 5770c59ff7298567cad01bb3ed4cc6a684b2378a
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373693"
---
# <a name="kusto-ingest-client-library"></a>Bibliothèque cliente de réception Kusto

## <a name="overview"></a>Vue d’ensemble
La bibliothèque Kusto. deréception est une bibliothèque .NET 4.6.2 qui permet d’envoyer des données au service Kusto.
Kusto. Adout prend des dépendances sur les bibliothèques et kits de développement logiciel suivants :

* ADAL pour l’authentification AAD
* Client de stockage Azure

Les méthodes d’ingestion Kusto sont définies par l’interface [IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient) et autorisent l’ingestion des données de Stream, IDataReader, des fichiers locaux et des objets BLOB Azure en mode synchrone et asynchrone.

## <a name="ingest-client-flavors"></a>Types de clients de réception
Conceptuellement, il existe deux versions de base du client de réception : en file d’attente et directe.

### <a name="queued-ingestion"></a>Réception en file d’attente
Défini par [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), ce mode limite la dépendance du code client sur le service Kusto. L’ingestion est effectuée en publiant un message d’ingestion Kusto dans une file d’attente Azure, qui est ensuite acquise auprès du service Kusto Gestion des données (ingestion). Tous les artefacts de stockage intermédiaires seront créés par le client de réception à l’aide des ressources allouées par Kusto Gestion des données service.

**Les avantages du mode de mise en file d’attente** incluent (mais ne sont pas limités à) :

* Découplage du processus d’ingestion de données à partir du service de moteur Kusto
* Autorise la conservation des demandes d’ingestion lorsque le service du moteur Kusto (ou ingestion) n’est pas disponible
* Permet une agrégation efficace et contrôlable des données entrantes par le service d’ingestion, ce qui améliore les performances.
* Autorise le service d’ingestion Kusto à gérer la charge d’ingestion sur le service de moteur Kusto
* Le service d’ingestion Kusto effectue une nouvelle tentative en cas d’échec de l’ingestion temporaire (par exemple, la limitation XStore)
* Fournit un mécanisme pratique pour suivre la progression et les résultats de chaque demande d’ingestion

Le diagramme suivant présente l’interaction du client d’ingestion en attente avec Kusto :

![texte de remplacement](../images/queued-ingest.jpg "en attente-réception")

### <a name="direct-ingestion"></a>Ingestion directe
Défini par IKustoDirectIngestClient, ce mode force l’interaction directe avec le service de moteur Kusto. Dans ce mode, le service d’ingestion Kusto ne gère pas ou ne gère pas les données. Chaque demande d’ingestion en mode direct est finalement traduite dans la `.ingest` commande exécutée directement sur le service du moteur Kusto.
Le diagramme suivant présente l’interaction du client d’ingestion directe avec Kusto :

![texte de remplacement](../images/direct-ingest.jpg "réception directe")

> [!NOTE]
> Le mode direct n’est pas recommandé pour les solutions d’ingestion des niveaux de production.

**Les avantages du mode direct sont les** suivants :

* Faible latence (il n’y a pas d’agrégation). Toutefois, une faible latence peut également être obtenue avec la réception en file d’attente
* Lorsque des méthodes synchrones sont utilisées, la saisie semi-automatique de la méthode indique la fin de l’opération d’ingestion

**Les inconvénients du mode direct sont les** suivants :

* Le code client doit implémenter toute logique de nouvelle tentative ou de gestion des erreurs
* Les ingestions sont impossibles lorsque le service du moteur Kusto n’est pas disponible
* Le code client peut saturer le service du moteur Kusto avec les demandes d’ingestion, car il n’est pas conscient de la capacité du service du moteur

## <a name="ingestion-best-practices"></a>Méthodes recommandées d’ingestion

### <a name="general"></a>Général
Les [meilleures pratiques](kusto-ingest-best-practices.md) d’ingestion fournissent le PDV et le débit PDV sur l’ingestion.

### <a name="thread-safety"></a>Sécurité des threads
Les implémentations du client de réception Kusto sont thread-safe et doivent être réutilisées. Il n’est pas nécessaire de créer une instance de `KustoQueuedIngestClient` classe pour chaque opération de réception, ou même plusieurs. Une seule instance de `KustoQueuedIngestClient` est requise par cluster Kusto cible par processus utilisateur. L’exécution de plusieurs instances est inefficace et peut faire fonctionner le cluster Gestion des données.

### <a name="supported-data-formats"></a>Formats de données pris en charge
Si vous utilisez l’ingestion native, si ce n’est déjà fait, téléchargez les données vers un ou plusieurs objets BLOB de stockage Azure. Les formats d’objets BLOB actuellement pris en charge sont décrits dans la rubrique [formats de données pris en charge](../../../ingestion-supported-formats.md) .

### <a name="schema-mapping"></a>Mappage de schéma
Les [mappages de schéma](../../management/mappings.md) facilitent la liaison déterministe des champs de données sources aux colonnes de la table de destination.

## <a name="usage-and-further-reading"></a>Utilisation et lectures supplémentaires

* Comme décrit ci-dessus, la base recommandée pour les solutions d’ingestion durables et à grande échelle pour Kusto doit être **KustoQueuedIngestClient**.
* Pour réduire la charge inutile sur votre service Kusto, il est recommandé d’utiliser une seule instance du client de réception Kusto (mise en file d’attente ou directe) par processus par cluster Kusto. L’implémentation du client de réception Kusto est thread-safe et entièrement réentrante.

### <a name="ingestion-permissions"></a>Autorisations d’ingestion
* Les autorisations d’ingestion [Kusto](kusto-ingest-client-permissions.md) expliquent la configuration des autorisations requises pour une ingestion réussie à l’aide du package Kusto. inréception

### <a name="kustoingest-library-reference"></a>Informations de référence sur la bibliothèque Kusto. deréception
* La [Référence du client Kusto. deréception](kusto-ingest-client-reference.md) contient une référence complète des interfaces et des implémentations du client de réception Kusto.<BR>Vous y trouverez des informations sur la façon de créer des clients de réception, d’augmenter les demandes d’ingestion, de gérer la progression de l’ingestion et bien plus encore
* [État de l’opération de Kusto.](kusto-ingest-client-status.md) ingestion explique les fonctionnalités **KustoQueuedIngestClient** pour le suivi de l’état d’ingestion
* [Kusto. deréception Errors](kusto-ingest-client-errors.md) documente les erreurs et les exceptions du client de réception Kusto
* Les [exemples Kusto.](kusto-ingest-client-examples.md) Desent présentent des extraits de code illustrant diverses techniques d’ingestion de données dans Kusto

### <a name="data-ingestion-rest-apis"></a>API REST d’ingestion de données
Ingestion de [données sans Kusto. la bibliothèque de réception](kusto-ingest-client-rest.md) explique comment implémenter l’ingestion de Kusto en file d’attente à l’aide des API REST Kusto et sans dépendre de la bibliothèque Kusto. ingestion.
