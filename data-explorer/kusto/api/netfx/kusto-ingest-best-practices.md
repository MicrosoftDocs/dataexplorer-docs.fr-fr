---
title: Meilleures pratiques pour la bibliothèque cliente de réception Kusto-Azure Explorateur de données
description: Cet article décrit les meilleures pratiques pour la bibliothèque cliente de réception Kusto.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: d02d030a6deb468a4804c68965466e1917858be9
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382333"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Bibliothèque cliente de réception Kusto-meilleures pratiques

## <a name="select-the-right-ingestclient-flavor"></a>Sélectionner la version IngestClient appropriée

Utilisez [KustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient), il s’agit du mode d’ingestion de données natif recommandé. Voici pourquoi :
* L’ingestion directe est impossible pendant le temps d’arrêt du moteur, par exemple lors du déploiement. Dans le mode de réception en file d’attente, les demandes sont conservées dans la file d’attente Azure, et le service Gestion des données réessaie si nécessaire.
* Le service Gestion des données empêche le moteur de surcharger les demandes d’ingestion. Le remplacement de ce contrôle à l’aide de l’ingestion directe, par exemple, peut affecter sérieusement l’ingestion du moteur et les performances des requêtes.
* Gestion des données regroupe plusieurs demandes d’ingestion. L’agrégation optimise la taille de la partition (étendue) initiale à créer.
* Il est facile d’obtenir des commentaires sur chaque ingestion.

## <a name="avoid-tracking-ingest-operation-status"></a>Éviter le suivi de l’état de l’opération de réception

Le suivi de l’état de l' [opération de réception](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) est utile. Toutefois, pour les flux de données de volume volumineux, l’activation des notifications positives pour chaque demande d’ingestion doit être évitée. Ce suivi place une charge extrême sur les ressources xStore sous-jacentes, ce qui peut entraîner une augmentation de la latence d’ingestion et même une non-réactivité du cluster.

## <a name="optimizing-for-throughput"></a>Optimisation pour le débit

L’ingestion fonctionne mieux si elle est effectuée dans de grands segments. 
* Il consomme le moins de ressources
* Il produit le partitions de données le plus performant et optimisé, et produit les meilleures transactions de données.

Nous recommandons aux clients qui envoient des données avec la bibliothèque Kusto. ingestion ou directement dans le moteur, d’envoyer des données par lots de **100 Mo** à **1 Go (non compressés)**
* La limite supérieure est importante lorsque vous travaillez directement avec le moteur, afin de réduire la quantité de mémoire utilisée par le processus d’ingestion 

> [!NOTE]
> Lors de l’utilisation de la `KustoQueuedIngestClient` classe, les blocs de données trop volumineux sont répartis en segments plus petits, et ces petits fragments sont agrégés, à un certain degré, avant d’atteindre le moteur.

* La limite inférieure de la taille des données ingérées est également importante, bien que moins critique. L’ingestion de données par petits lots tous les instants et est parfaitement correcte, bien que légèrement moins efficace que l’utilisation de lots volumineux. La `KustoQueuedIngestClient` classe résout également le problème pour les clients qui ont besoin d’ingérer de grandes quantités de données et ne peut pas les traiter par lots dans de grands segments avant de les envoyer au moteur.

## <a name="other-factors-that-impact-ingestion-throughput"></a>Autres facteurs qui ont un impact sur le débit d’ingestion

Plusieurs facteurs peuvent affecter le débit de réception. Lorsque vous planifiez votre pipeline d’ingestion, veillez à évaluer les points suivants, qui peuvent avoir des implications significatives sur votre CMV.

| Facteur à prendre en compte |  Description                                                                                              |
|--------------------------|-----------------------------------------------------------------------------------------------------------|
| Format de données              | Le format CSV est le format le plus rapide pour la réception. En général, JSON prend plus de 2 ou 3 fois pour le même volume de données.|
| Largeur du tableau              | Veillez à ne pas recevoir les données dont vous avez vraiment besoin. Plus la table est grande, plus le nombre de colonnes doit être encodé et indexé, et plus le débit est faible. Vous pouvez contrôler les champs qui sont ingérés, en fournissant un mappage d’ingestion.       |
| Emplacement des données sources     | Évitez les lectures entre régions pour accélérer l’ingestion.                                                       |
| Charger sur le cluster      | Lorsqu’un cluster subit une charge de requête élevée, les ingestions prennent plus de temps, ce qui réduit le débit.|

## <a name="optimizing-for-cogs"></a>Optimisation pour les COGS

L’utilisation de bibliothèques clientes Kusto pour la réception de données dans Azure Explorateur de données reste la solution la plus économique et la plus robuste. Nous recommandons à nos clients de passer en revue leurs méthodes d’ingestion et de tirer parti de la tarification Azure Storage qui rendra les transactions BLOB beaucoup plus rentables.

* **Limitez le nombre de segments de données ingérés**.
    Pour mieux contrôler vos coûts d’ingestion d’Azure Explorateur de données et réduire votre facture mensuelle, limitez le nombre de segments de données ingérés (fichiers/objets BLOB/flux).
* **Réception de segments de données volumineux (jusqu’à 1 Go de données non compressées)**. 
    De nombreuses équipes essaient d’atteindre une faible latence en informant des dizaines de millions de petites portions de données, ce qui est inefficace et coûteux. 
* **Traitement par lot**. Toute quantité de traitement par lot côté client améliore l’optimisation. 
* **Fournissez au client Kusto. unout une taille de données exacte, non compressée**.
    Si ce n’est pas le cas, vous risquez de provoquer des transactions de stockage supplémentaires.
* **Évitez** d’envoyer des données pour l’ingestion avec l' `FlushImmediately` indicateur défini sur `true` . Évitez également d’envoyer de petits segments avec des `ingest-by` / `drop-by` balises définies. Si vous utilisez ces méthodes, elles :
     * empêcher le service d’agréger correctement les données pendant l’ingestion
     * entraîner des transactions de stockage inutiles après l’ingestion
     * affecter les COGS
     * entraîner une dégradation des performances d’ingestion ou de requête de votre cluster, s’il est utilisé de façon excessive
