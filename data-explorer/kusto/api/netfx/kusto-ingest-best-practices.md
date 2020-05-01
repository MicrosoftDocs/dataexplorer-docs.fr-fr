---
title: Bibliothèque cliente de réception Kusto-meilleures pratiques-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la bibliothèque cliente de réception Kusto-meilleures pratiques dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: 2369d352e316f1d9b49de71fb197507280598754
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617933"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Bibliothèque cliente de réception Kusto-meilleures pratiques

## <a name="choosing-the-right-ingestclient-flavor"></a>Choix de la version IngestClient appropriée

L’utilisation de [KustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) est le mode d’ingestion de données natif recommandé. Voici pourquoi :
* L’ingestion directe est impossible pendant le temps d’arrêt du moteur (par exemple, pendant le déploiement), tandis que dans le mode d’ingestion en attente, les demandes sont conservées dans la file d’attente Azure, et le service Gestion des données réessaie en fonction des besoins.
* Le service Gestion des données est chargé de ne pas surcharger le moteur avec les demandes d’ingestion. Le remplacement de ce contrôle (par exemple, à l’aide de l’ingestion directe) peut affecter sérieusement les performances du moteur, à la fois l’ingestion et la requête.
* Gestion des données agrège plusieurs demandes d’ingestion afin d’optimiser la taille de la partition (étendue) initiale à créer.
* Il est facile d’obtenir des commentaires sur chaque ingestion.

## <a name="tracking-ingest-operation-status"></a>État de l’opération de suivi de réception

Le suivi de l’état de l' [opération de réception](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) est une fonctionnalité utile. Toutefois, l’activation de la création de rapports pour la réussite peut être facilement déconseillée dans la mesure où elle risque de paralyser votre service.

> [!WARNING]
> L’activation des notifications positives pour chaque demande d’ingestion des flux de données de volume volumineux doit être évitée, car cela entraîne une charge extrême sur les ressources xStore sous-jacentes. La charge supplémentaire peut entraîner une augmentation de la latence d’ingestion et même une non-réactivité du cluster.

## <a name="optimizing-for-throughput"></a>Optimisation pour le débit

* L’ingestion fonctionne mieux si elle est effectuée dans de grands segments. Il consomme le moins de ressources, produit le partitions de données le plus performant et optimisé, et génère les artefacts de données les plus performants. En général, nous recommandons aux clients qui envoient des données avec la bibliothèque Kusto. ingestion ou directement dans le moteur, d’envoyer des données par lots de **100 Mo à 1 Go (non compressés)**
* La limite supérieure est importante lorsque vous travaillez directement avec le moteur, afin de réduire la quantité de mémoire utilisée par le processus d’ingestion 

> [!NOTE]
> Lors de l' `KustoQueuedIngestClient` utilisation de la classe, les blocs de données trop volumineux sont répartis en segments plus petits, et ces petits fragments sont agrégés, à un certain degré, avant d’atteindre le moteur.

* La limite inférieure de la taille des données ingérées est également importante, bien que moins critique. L’ingestion de données par petits lots tous les instants et est parfaitement correcte, bien que légèrement moins efficace que l’utilisation de lots volumineux. La `KustoQueuedIngestClient` classe résout également le problème pour les clients qui ont besoin d’ingérer de grandes quantités de données et ne peut pas les traiter par lots dans de grands segments avant de les envoyer au moteur.

## <a name="factors-impacting-ingestion-throughput"></a>Facteurs ayant un impact sur le débit d’ingestion

Plusieurs facteurs peuvent affecter le débit de réception. Lorsque vous planifiez votre pipeline d’ingestion, veillez à évaluer les points suivants, qui peuvent avoir des implications significatives sur votre CMV.

| Facteur à prendre en compte |  Decription                                                                                               |
|--------------------------|-----------------------------------------------------------------------------------------------------------|
| Format de données              | Le format CSV est le format le plus rapide pour la réception, JSON prend généralement 2x ou 3 fois plus pour le même volume de données.|
| Largeur du tableau              | Veillez à ne pas recevoir les données dont vous avez vraiment besoin. Plus la table est grande, plus les colonnes doivent être encodées et indexées, et par conséquent, le débit est inférieur. Vous pouvez contrôler les champs qui sont ingérés, en fournissant un mappage d’ingestion.|
| Emplacement des données sources     | Évitez les lectures entre régions pour accélérer l’ingestion.                                                       |
| Charger sur le cluster      | Lorsqu’un cluster subit une charge de requête élevée, les ingestions prennent plus de temps, ce qui réduit le débit.|
| Modèle d’ingestion        | L’ingestion est à son niveau optimal lorsque le cluster est traité avec des lots allant jusqu’à 1 Go ( `KustoQueuedIngestClient`gérés à l’aide de). |

## <a name="optimizing-for-cogs"></a>Optimisation pour les COGS

L’utilisation de bibliothèques clientes Kusto pour la réception de données dans Kusto reste la solution la plus économique et la plus robuste. Nous recommandons à nos clients de passer en revue leurs méthodes d’ingestion et de tirer parti de la tarification Azure Storage qui rendra les transactions BLOB beaucoup plus rentables.

Pour mieux contrôler vos coûts d’ingestion d’Azure Explorateur de données et réduire votre facture mensuelle, limitez le nombre de segments de données ingérés (fichiers/objets BLOB/flux).

* **Préférer la réception de segments de données plus volumineux (jusqu’à 1 Go de données non compressées)**. 
    De nombreuses équipes essaient d’atteindre une faible latence en informant des dizaines de millions de petites portions de données, ce qui est extrêmement inefficace et très coûteux. Toute quantité de traitement par lot côté client peut être utile. 
* Assurez- **vous que vous fournissez au client Kusto. unout une taille de données précise et non compressée**.
    Si ce n’est pas le cas, vous risquez de provoquer des transactions de stockage supplémentaires.
* **Évitez** d’envoyer des données pour l’ingestion `FlushImmediately` avec l' `true`indicateur défini sur ou d’envoyer des `ingest-by` / `drop-by` petits segments avec des balises définies.
    L’utilisation de ces méthodes empêche le service d’agréger correctement les données pendant l’ingestion et entraîne des transactions de stockage inutiles après l’ingestion, affectant ainsi la COGS.
    
    En outre, l’utilisation excessive de ces derniers peut entraîner une dégradation des performances d’ingestion et/ou de requête de votre cluster.
    
