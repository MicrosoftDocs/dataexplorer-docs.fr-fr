---
title: Bibliothèque cliente de réception Kusto-Azure Explorateur de données
description: Cet article décrit la bibliothèque cliente de réception Kusto dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 03/18/2020
ms.openlocfilehash: c5a0bd91df6e12d90436e3b27a2b55021668117a
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874339"
---
# <a name="kusto-ingest-client-library"></a>Bibliothèque cliente de réception Kusto 

`Kusto.Ingest` la bibliothèque est une bibliothèque .NET 4.6.2 permettant d’envoyer des données au service Kusto.
Il prend des dépendances sur les bibliothèques et kits de développement logiciel suivants :

* ADAL pour l’authentification Azure AD
* Client de stockage Azure

Les méthodes d’ingestion sont définies par l’interface [IKustoIngestClient](kusto-ingest-client-reference.md#interface-ikustoingestclient) .  Les méthodes gèrent l’ingestion des données de Stream, IDataReader, des fichiers locaux et des objets BLOB Azure en modes synchrones et asynchrones.

## <a name="ingest-client-flavors"></a>Types de clients de réception

Il existe deux versions de base du client de réception : en file d’attente et directe.

### <a name="queued-ingestion"></a>Ingestion en file d’attente

Le mode de réception en file d’attente, défini par [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), limite la dépendance du code client sur le service Kusto. L’ingestion est effectuée en publiant un message d’ingestion Kusto dans une file d’attente Azure, qui est ensuite acquise auprès du service Kusto Gestion des données (ingestion). Tout élément de stockage intermédiaire sera créé par le client de réception à l’aide des ressources du service de Gestion des données Kusto.

**Les avantages du mode de mise en file d’attente sont les suivants :**

* Dissocie le processus d’ingestion de données du service de moteur Kusto
* Permet aux demandes d’ingestion d’être rendues persistantes quand le service du moteur Kusto (ou ingestion) n’est pas disponible
* Améliore les performances grâce à l’agrégation efficace et contrôlable des données entrantes par le service d’ingestion 
* Permet au service d’ingestion Kusto de gérer la charge d’ingestion sur le service de moteur Kusto
* Réessaye le service d’ingestion Kusto, si nécessaire, sur les échecs d’ingestion temporaires, comme pour la limitation XStore
* Fournit un mécanisme pratique pour suivre la progression et les résultats de chaque demande d’ingestion

Le diagramme suivant présente l’interaction du client d’ingestion en attente avec Kusto :

:::image type="content" source="../images/about-kusto-ingest/queued-ingest.png" alt-text="en attente-réception":::
 
### <a name="direct-ingestion"></a>Ingestion directe

Le mode d’ingestion directe, défini par IKustoDirectIngestClient, force l’interaction directe avec le service de moteur Kusto. Dans ce mode, le service d’ingestion Kusto ne gère pas les données par modération. Chaque demande d’ingestion est traduite dans la `.ingest` commande qui est exécutée directement sur le service du moteur Kusto.

Le diagramme suivant présente l’interaction du client d’ingestion directe avec Kusto :

:::image type="content" source="../images/about-kusto-ingest/direct-ingest.png" alt-text="réception directe":::

> [!NOTE]
> Le mode direct n’est pas recommandé pour les solutions d’ingestion des niveaux de production.

**Les avantages du mode direct sont les suivants :**

* Faible latence et aucune agrégation. Toutefois, une faible latence peut également être obtenue avec la réception en file d’attente
* Lorsque des méthodes synchrones sont utilisées, la saisie semi-automatique de la méthode indique la fin de l’opération d’ingestion

**Les inconvénients du mode direct sont les suivants :**

* Le code client doit implémenter toute logique de nouvelle tentative ou de gestion des erreurs
* Les ingestions sont impossibles lorsque le service du moteur Kusto n’est pas disponible
* Le code client peut saturer le service du moteur Kusto avec les demandes d’ingestion, car il n’est pas conscient de la capacité du service du moteur

## <a name="ingestion-best-practices"></a>Méthodes recommandées d’ingestion

Les [meilleures pratiques](kusto-ingest-best-practices.md) d’ingestion fournissent le PDV et le débit PDV sur l’ingestion.

* **Sécurité des threads-** Les implémentations du client de réception Kusto sont thread-safe et doivent être réutilisées. Il n’est pas nécessaire de créer une instance de `KustoQueuedIngestClient` classe pour chaque opération d’ingestion. Une seule instance de `KustoQueuedIngestClient` est requise par cluster Kusto cible par processus utilisateur. L’exécution de plusieurs instances est une opération de compteur de productivité et peut entraîner des dénis de service sur le cluster Gestion des données.

* **Formats de données pris en charge-** Si vous utilisez l’ingestion native, si ce n’est déjà fait, téléchargez les données vers un ou plusieurs objets BLOB de stockage Azure. Les formats BLOB actuellement pris en charge sont documentés sous [formats de données pris en charge](../../../ingestion-supported-formats.md).

* **Mappage de schéma-** 
 Les [mappages de schéma](../../management/mappings.md) facilitent la liaison déterministe des champs de données sources aux colonnes de la table de destination.

* **Autorisations** d’ingestion- 
 Les autorisations d’ingestion [Kusto](kusto-ingest-client-permissions.md) expliquent la configuration des autorisations requises pour une ingestion réussie à l’aide du `Kusto.Ingest` Package.

* **Utilisation-** Comme décrit précédemment, la base recommandée pour les solutions d’ingestion durables et à grande échelle pour Kusto doit être **KustoQueuedIngestClient**.
Pour réduire la charge inutile sur votre service Kusto, nous vous recommandons d’utiliser une seule instance du client de réception Kusto (mise en file d’attente ou directe) par processus, par cluster Kusto. L’implémentation du client de réception Kusto est thread-safe et entièrement réentrante.

## <a name="next-steps"></a>Étapes suivantes

* La [Référence du client Kusto. deréception](kusto-ingest-client-reference.md) contient une référence complète des interfaces et des implémentations du client de réception Kusto. Vous y trouverez des informations sur la façon de créer des clients d’ingestion, d’augmenter les demandes d’ingestion, de gérer la progression de l’ingestion et bien plus encore

* [État de l’opération de Kusto.](kusto-ingest-client-status.md) ingestion explique les fonctionnalités KustoQueuedIngestClient pour le suivi de l’état d’ingestion

* Les [Erreurs Kusto. deréception](kusto-ingest-client-errors.md) décrivent les exceptions et les erreurs du client de réception Kusto

* Les [exemples Kusto. deréception](kusto-ingest-client-examples.md) montrent des extraits de code qui illustrent diverses techniques d’ingestion de données dans Kusto

* L’ingestion de [données sans Kusto. Inréception Library](kusto-ingest-client-rest.md) explique comment implémenter l’ingestion de Kusto en file d’attente, à l’aide des API REST Kusto et sans dépendre de la `Kusto.Ingest` bibliothèque.

