---
title: Kusto Ingest Client Library - Meilleures pratiques - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto Ingest Client Library - Best Practices in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/16/2019
ms.openlocfilehash: e28ec3f99abe4163b83721d753da98c0035d1e46
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523696"
---
# <a name="kusto-ingest-client-library---best-practices"></a>Bibliothèque de clients Kusto Ingest - Meilleures pratiques

## <a name="choosing-the-right-ingestclient-flavor"></a>Choisir la bonne saveur IngestClient
L’utilisation [de KustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) est le mode d’ingestion de données indigène recommandé. Voici pourquoi :
* L’ingestion directe est impossible pendant les temps d’arrêt du moteur Kusto (par exemple pendant le déploiement), tandis que dans le mode d’ingestion en file d’attente, les demandes sont persistées dans la file d’attente Azure et le service de gestion des données sera retenti au besoin.
* Le service de gestion des données est responsable de ne pas surcharger le moteur avec des demandes d’ingestion. Le dépassement de ce contrôle (p. ex. l’utilisation de l’ingestion directe) pourrait avoir une incidence grave sur les performances du moteur, tant l’ingestion que la requête.
* Data Management regroupe plusieurs demandes d’ingestion afin d’optimiser la taille de l’éclat initial (étendue) à créer.
* Il existe un moyen pratique d’obtenir des commentaires sur chaque ingestion - qu’elle soit réussie ou non.

## <a name="tracking-ingest-operation-status"></a>Suivi de l’état de fonctionnement des ingéres
[Suivi de l’état de fonctionnement des ingérests](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) est une fonctionnalité utile dans Kusto, cependant, l’allumer pour le succès de rapports peut être facilement abusé dans la mesure où il paralysera votre service.<BR>

> [!WARNING]
> Il faut éviter d’allumer des notifications positives pour chaque demande d’ingestion pour les flux de données à grand volume, car cela met une charge extrême sur les ressources sous-jacentes de xStore, > ce qui pourrait entraîner une latence accrue de l’ingestion et même une non-réactivité complète du cluster.

## <a name="optimizing-for-throughput"></a>Optimisation du débit
* L’ingestion fonctionne mieux (c’est-à-dire qu’elle consomme le moins de ressources pendant l’ingestion, produit les éclats de données les plus optimisés par COGS et se traduit par les artefacts de données les plus performants) si elle est effectuée en gros morceaux. En général, nous recommandons aux clients qui ingèrent des données avec la bibliothèque Kusto.Ingest ou directement dans le moteur d’envoyer des données en lots de **100 Mo à 1 Go (non compressé)**
* La limite supérieure est importante lorsque vous travaillez directement avec le moteur Kusto pour `KustoQueuedIngestClient` aider à réduire la quantité de mémoire utilisée par le processus d’ingestion (lors de l’utilisation de la classe, des blocs de données trop grands seront divisés sur le client en portions plus petites, et de petits morceaux seront agrégés dans une certaine mesure, avant d’atteindre le moteur Kusto)
* La limite inférieure de la taille des données ingérées est également importante, bien que moins critique. Ingestion de données en petits lots de temps en temps est parfaitement fine, bien que légèrement moins efficace que l’utilisation de gros lots. `KustoQueuedIngestClient`classe résout également le problème pour les clients qui ont besoin d’ingérer de grandes quantités de données et ne peuvent pas les regrouper en gros morceaux avant de les envoyer à Kusto

## <a name="factors-impacting-ingestion-throughput"></a>Facteurs ayant une incidence sur le débit d’ingestion
De multiples facteurs peuvent influer sur le débit d’ingestion. Lors de la planification de votre pipeline d’ingestion Kusto, assurez-vous d’évaluer les points suivants, ce qui peut avoir des implications importantes sur vos COG.
* Format de données - CSV est le format le plus rapide à ingérer, JSON prendra généralement x2 ou x3 plus longtemps pour le même volume de données
* Largeur de la table - assurez-vous que vous n’ingériez les données dont vous avez vraiment besoin, car plus la table est large, plus les colonnes seront codées et indexées, par conséquent, plus le débit est faible.
    Vous pouvez contrôler quels champs sont ingérés en fournissant une cartographie d’ingestion.
* Emplacement des données de source - éviter les lectures interrésaliques accélère l’ingestion
* Chargement sur le cluster - lorsque le cluster connaît une charge de requête élevée, les ingestions prendront plus de temps à remplir, réduisant le débit
* Modèle d’ingestion - l’ingestion est dans son optimum lorsque le cluster est `KustoQueuedIngestClient`servi avec des lots allant jusqu’à 1 Go (pris en charge par l’utilisation )

## <a name="optimizing-for-cogs"></a>Optimisation pour COGS
Tout en utilisant les bibliothèques client De Kusto afin d’ingériser les données dans Kusto reste l’option la moins chère et la plus robuste, nous exhortons nos clients à revoir leurs tactiques d’ingestion et de prendre en considération les récents changements (automne 2017) dans les prix de stockage Azure, qui a rendu les transactions blob significativement (x100) plus coûteux.
<BR>
Il est important de comprendre que plus vous envoyez de données (fichiers/blobs/streams) à Kusto, plus votre facture mensuelle sera importante.
Si vous suivez les recommandations suivantes, vous serez en mesure de mieux contrôler vos coûts d’ingestion Kusto :
* **Préférez l’ingestion de plus gros morceaux de données (jusqu’à 1 Go de données non compressées)**<br>
    De nombreuses équipes tentent d’atteindre une faible latence en ingérant des dizaines de millions de minuscules morceaux de données, ce qui est extrêmement inefficace et très coûteux.<br>
    Tout lotage du côté client aiderait. 
* **Assurez-vous de fournir au client Kusto.Ingest une taille de données non compressée**<br>
    Ne pas le faire peut amener Kusto à effectuer des transactions de stockage supplémentaires.
* **Évitez** d’envoyer des données `FlushImmediately` pour `true`l’ingestion avec `ingest-by` / `drop-by` le drapeau réglé à , ou d’envoyer de petits morceaux avec des étiquettes définies.<br>
    L’utilisation de ces éléments empêche le service Kusto d’agréger correctement les données pendant l’ingestion, et provoque des transactions de stockage inutiles après l’ingestion, affectant COGS.<br>
    En outre, l’utilisation de ces excessivement pourrait entraîner une ingestion dégradée et / ou des performances de requête dans votre cluster.<br>
    