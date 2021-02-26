---
title: Nouveautés d’Azure Data Explorer
description: Nouveautés de la documentation d’Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/17/2021
ms.openlocfilehash: b3cc6d5562f7332723ba63edf5a07a89578f95a2
ms.sourcegitcommit: 24efef3c1d64ddc57ac449d5e9332f472f93b466
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100655303"
---
# <a name="whats-new-in-azure-data-explorer"></a>Nouveautés d’Azure Data Explorer

Bienvenue dans les nouveautés d’Azure Data Explorer. Cet article détaille le contenu nouveau et mis à jour de façon significative dans la documentation Azure Data Explorer.

## <a name="january-2021"></a>Janvier 2021

Cette section liste les principales modifications apportées à la documentation en janvier 2021.

### <a name="general"></a>Général

Titre de l’article | Description
---|--- 
[Contrôles de conformité réglementaire Azure Policy pour Azure Data Explorer](security-controls-policy.md) | Nouvel article. Cette page liste les **domaines de conformité** et les **contrôles de sécurité** pour Azure Data Explorer.
[Autoriser les requêtes et les commandes interlocataires](cross-tenant-query-and-commands.md) | Nouvel article. Dans cet article, vous allez apprendre à accorder au cluster l’accès aux principaux à partir d’un autre locataire.

### <a name="management"></a>Gestion

### <a name="new-articles"></a>Nouveaux articles

Titre de l’article | Description
---|---
[Commandes de nettoyage des conteneurs d’extension](kusto/management/clean-extent-containers.md) | Nouvel article. Cet article décrit les commandes `.clean databases extentcontainers` et `.show database extentcontainers clean operations` dans Azure Data Explorer.
[Stratégie de classification des demandes (préversion)](kusto/management/request-classification-policy.md)  <br>[Stratégie de classification des demandes (préversion) - Commandes de contrôle](kusto/management/request-classification-policy-commands.md) | Nouveaux articles. Le processus de classification affecte les demandes entrantes à un groupe de charges de travail en fonction des caractéristiques des demandes.
[Stratégie de limitation des demandes (préversion)](kusto/management/request-limits-policy.md) | Nouvel article. La stratégie de limitation des demandes d’un groupe de charges de travail permet de limiter les ressources utilisées par la demande pendant son exécution.
[Stratégie de limitation du nombre de demandes (préversion)](kusto/management/request-rate-limit-policy.md) | Nouvel article. La stratégie de limitation du nombre de demandes du groupe de charges de travail vous permet de limiter le nombre de demandes simultanées classifiées dans le groupe de charge de travail.
[Groupes de charges de travail (préversion)](kusto/management/workload-groups.md)  <br> [Groupes de charge de travail (préversion) - Commandes de contrôle](kusto/management/workload-groups-commands.md) | Nouveaux articles. Un groupe de charges de travail sert de conteneur pour les demandes (requêtes, commandes) qui ont des critères de classification similaires. Une charge de travail permet une supervision globale des demandes et définit des stratégies pour les demandes.
[Gestion des requêtes](kusto/management/queries.md) | Article mis à jour. Syntaxe mise à jour

## <a name="december-2020"></a>Décembre 2020

Cette section liste les principales modifications apportées à la documentation en décembre 2020.

### <a name="general"></a>Général

Titre de l’article | Description
---|---
[Codes d’erreur d’ingestion dans Azure Data Explorer](error-codes.md) | Nouvel article. Cette liste contient les codes d’erreur que vous pouvez rencontrer pendant l’[ingestion](ingest-data-overview.md).

### <a name="management"></a>Gestion

Titre de l’article | Description
---|---
[.create table based-on](kusto/management/create-table-based-on-command.md)  | Nouvel article. Crée une table vide basée sur une table existante.
[Résultats de requête stockés (préversion)](kusto/management/stored-query-results.md) | Nouvel article. Les résultats de requête stockés sont un mécanisme qui stocke temporairement le résultat d’une requête sur le service. 
[Créer et modifier des tables externes dans Stockage Azure ou Azure Data Lake](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake.md) | Article mis à jour. Documentation des options de définition de table externe `filesPreview` et `dryRun`

### <a name="functions-library"></a>Bibliothèque de fonctions

Titre de l’article | Description
---|---
[series_metric_fl()](kusto/functions-library/series-metric-fl.md) | Nouvel article. La fonction `series_metric_fl()` sélectionne et récupère des séries chronologiques de métriques ingérées dans Azure Data Explorer en utilisant le système de supervision Prometheus.
[series_rate_fl()](kusto/functions-library/series-rate-fl.md) | Nouvel article. La fonction `series_rate_fl()` calcule la vitesse moyenne d’augmentation d’une métrique par seconde.
[series_fit_lowess_fl()](kusto/functions-library/series-fit-lowess-fl.md) | Nouvel article. La fonction `series_fit_lowess_fl()` applique une régression LOWESS sur une série.
[Exporter des données dans une table externe](/azure/data-explorer/kusto/management/data-export/export-data-to-an-external-table.md) | Article mis à jour. Nouvelle syntaxe pour les tables externes dans la documentation sur l’exportation

## <a name="november-2020"></a>Novembre 2020

Cette section liste les principales modifications apportées à la documentation en novembre 2020.

### <a name="general"></a>Général

Titre de l’article | Description
---|---
[Définitions intégrées d’Azure Policy pour Azure Data Explorer](policy-reference.md) | Nouvel article. Index des définitions de stratégie intégrées d’[Azure Policy](/azure/governance/policy/overview) pour Azure Data Explorer. 
[Utiliser l’ingestion en un clic afin de créer une connexion de données Event Hub pour Azure Data Explorer](one-click-event-hub.md) | Nouvel article. Connecter un hub d’événements à une table dans Azure Data Explorer en utilisant l’[ingestion en un clic](ingest-data-one-click.md).
| [Configurer des identités managées pour votre cluster Azure Data Explorer](managed-identities.md) | Article mis à jour. Prend en charge les identités managées affectées par l’utilisateur et les identités managées affectées par le système
| [Créer une table dans Azure Data Explorer](one-click-table.md) | Article mis à jour. Disponibilité générale. |
 | [Démarrage rapide : Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md) | Article mis à jour. Nouvelles fonctionnalités.
|  [Présentation de l’ingestion en un clic](ingest-data-one-click.md) | Article mis à jour. Ajout de l’ingestion à partir de niveaux imbriqués JSON. Disponibilité générale.
| [Personnaliser les visuels du tableau de bord Azure Data Explorer](dashboard-customize-visuals.md) | Article mis à jour. Nouveaux visuels du tableau de bord et changements de paramètres.

### <a name="query"></a>Requête

Titre de l’article | Description
---|---
[Plug-in mysql_request (préversion)](./kusto/query/mysqlrequest-plugin.md) | Nouvel article. Le plug-in `mysql_request` envoie une requête SQL à un point de terminaison réseau MySQL Server et retourne le premier ensemble de lignes des résultats. 
[Plug-in ipv4_lookup](./kusto/query/ipv4-lookup-plugin.md) | Nouvel article. Le plug-in `ipv4_lookup` recherche une valeur IPv4 dans une table de recherche et retourne des lignes avec des valeurs correspondantes.
[ipv4_is_private()](./kusto/query/ipv4-is-privatefunction.md) | Nouvel article. Vérifie si l’adresse de la chaîne IPv4 appartient à un ensemble d’adresses IP de réseau privé.
[Mappage du langage de requête Splunk avec Kusto](./kusto/query/splunk-cheat-sheet.md) | Nouvel article. Cet article a pour but d’aider les utilisateurs qui connaissent Splunk à apprendre le langage de requête Kusto pour écrire des requêtes de journal avec Kusto. 
[gzip_compress_to_base64_string()](./kusto/query/gzip-base64-compress.md) | Nouvel article. Effectue la compression gzip et encode le résultat en base64.
[gzip_decompress_from_base64_string()](./kusto/query/gzip-base64-decompress.md) | Nouvel article. Décode la chaîne d’entrée en base64 et effectue une décompression gzip.
[array_reverse()](./kusto/query/array-reverse-function.md) | Nouvel article. Inverse l’ordre des éléments dans un tableau dynamique.

### <a name="management"></a>Gestion

Titre de l’article | Description
---|---
[.disable plugin](./kusto/management/disable-plugin.md) | Nouvel article. Désactive un plug-in.
[.enable plugin](./kusto/management/enable-plugin.md) | Nouvel article. Active un plug-in.
[.show plugins](./kusto/management/show-plugins.md) | Nouvel article. Liste tous les plug-ins du cluster.
| [Commandes d’abonnés de cluster](kusto/management/cluster-follower.md) | Article mis à jour. Syntaxe modifiée, ajout de `.alter follower database prefetch-extents`. |

### <a name="functions-library"></a>Bibliothèque de fonctions

Titre de l’article | Description
---|---
[series_downsample_fl()](./kusto/functions-library/series-downsample-fl.md) | La fonction `series_downsample_fl()` [ sous-échantillonne une série chronologique selon un facteur de type entier](https://en.wikipedia.org/wiki/Downsampling_(signal_processing)#Downsampling_by_an_integer_factor). 
[series_exp_smoothing_fl()](./kusto/functions-library/series-exp-smoothing-fl.md) | Applique un filtre de lissage exponentiel de base sur une série.

## <a name="october-2020"></a>Octobre 2020

Cette section liste les principales modifications apportées à la documentation en octobre 2020.

### <a name="general"></a>Général

 Titre de l’article | Description
---|---
[Ingérer des données à l’aide du kit SDK Java Azure Data Explorer](java-ingest-data.md) | Nouvel article.  Dans cet article, vous allez découvrir comment ingérer des données à l’aide de la bibliothèque Java d’Azure Data Explorer. 
[Créer manuellement des ressources pour l’ingestion Event Grid](ingest-data-event-grid-manual.md) | Nouvel article. Dans cet article, vous découvrez comment créer manuellement les ressources nécessaires à l’ingestion Event Grid : abonnement Event Grid, espace de noms Event Hub et hub d’événements. 
[Créer un point de terminaison privé ou de service vers Event Hub et Stockage Azure](vnet-endpoint-storage-event-hub.md) | Nouvel article. Un [point de terminaison privé](/azure/private-link/private-endpoint-overview) utilise une adresse IP de l’espace d’adressage de votre réseau virtuel afin que le service Azure établisse une connexion sécurisée entre Azure Data Explorer et des services Azure tels que Stockage Azure et Event Hub.  
[EngineV3 - préversion](engine-v3.md) | Nouvel article. Kusto EngineV3 est le moteur de stockage et de requête nouvelle génération d’Azure Data Explorer. 
[Créer un cluster et une base de données Azure Data Explorer à l’aide de Go](create-cluster-database-go.md) |  Nouvel article. Dans cet article, vous allez créer un cluster et une base de données Azure Data Explorer à l’aide de [Go](https://golang.org/). 
[Créer une application Power Apps pour interroger des données dans Azure Data Explorer (préversion)](power-apps-connector.md) | Nouvel article. Dans cet article, vous allez créer une application Power Apps pour interroger des données Azure Data Explorer. 
|[Application logique Microsoft et Azure Data Explorer](kusto/tools/logicapps.md) |Article mis à jour. Disponibilité générale.
| [Utiliser les recommandations d’Azure Advisor pour optimiser votre cluster Azure Data Explorer](azure-advisor.md) | Article mis à jour. Nouvelle recommandation d’Azure Advisor.
| [Utiliser une base de données d’abonné pour joindre des bases de données dans Azure Data Explorer](follower.md) | Article mis à jour. Utiliser PowerShell pour attacher et détacher des bases de données d’abonnés. 

### <a name="query"></a>Requête

Titre de l’article | Description
---|---
[project-keep, opérateur](./kusto/query/project-keep-operator.md) | Nouvel article. Sélectionner les colonnes de l’entrée à conserver dans la sortie.
[bag_remove_keys()](./kusto/query/bag-remove-keys-function.md) | Nouvel article. Supprime des clés et les valeurs associées d’un conteneur de propriétés `dynamic`.
[series_fit_poly()](./kusto/query/series-fit-poly-function.md) | Nouvel article. Applique une régression polynomiale d’une variable indépendante (x_series) à une variable dépendante (y_series). 
[array_sort_asc()](./kusto/query/arraysortascfunction.md) | Nouvel article. Reçoit un ou plusieurs tableaux. Trie le premier tableau par ordre croissant. Classe les tableaux restants pour qu’ils correspondent au premier tableau retrié.
[array_sort_desc()](./kusto/query/arraysortdescfunction.md) | Nouvel article. Reçoit un ou plusieurs tableaux. Trie le premier tableau par ordre décroissant. Classe les tableaux restants pour qu’ils correspondent au premier tableau retrié.

### <a name="management"></a>Gestion

 Titre de l’article | Description
---|---
[Commandes d’une stratégie de limitation des requêtes](./kusto/management/query-throttling-policy-commands.md) | Nouvel article. La [stratégie de limitation des requêtes](kusto/management/query-throttling-policy.md) est une stratégie au niveau du cluster qui limite la simultanéité des requêtes dans le cluster. 
[Stratégie de limitation des requêtes](./kusto/management/query-throttling-policy.md) | Nouvel article. Définir la stratégie de limitation des requêtes pour limiter le nombre de requêtes simultanées que le cluster peut exécuter en même temps. 
[.clear table data](./kusto/management/clear-table-data-command.md) | Nouvel article. Efface les données d’une table existante, y compris les données d’ingestion de streaming.
|  [Commande de stratégie row_level_security](kusto/management/row-level-security-policy.md) <br> [Sécurité au niveau des lignes](kusto/management/rowlevelsecuritypolicy.md) |Articles mis à jour. Disponibilité générale.

### <a name="functions-library"></a>Bibliothèque de fonctions

 Titre de l’article | Description
---|---
[kmeans_fl()](./kusto/functions-library/kmeans-fl.md) | Nouvel article.  La fonction `kmeans_fl()` partitionne un jeu de données en utilisant l’[algorithme k-moyennes](https://en.wikipedia.org/wiki/K-means_clustering).
[series_dot_product_fl()](./kusto/functions-library/series-dot-product-fl.md) | Nouvel article. Calcule le produit scalaire de deux vecteurs numériques.

## <a name="september-2020"></a>Septembre 2020

Cette section liste les principales modifications apportées à la documentation en septembre 2020.

#### <a name="general"></a>Général

 Titre de l’article | Description
---|---
[Créer une table cible dans Azure Data Explorer (préversion)](one-click-table.md) | Nouvel article.  L’article suivant montre comment créer une table et une mise en correspondance du schéma rapidement et facilement à l’aide de l’interface utilisateur web d’Azure Data Explorer. 
[Activer le calcul isolé sur votre cluster Azure Data Explorer](isolated-compute.md) | Nouvel article. Les machines virtuelles de calcul isolé permettent aux clients d’exécuter leur charge de travail dans un environnement isolé du matériel dédié à un client unique.
[Utiliser les recommandations d’Azure Advisor pour optimiser votre cluster Azure Data Explorer (préversion)](azure-advisor.md) | Nouvel article.  Azure Advisor analyse les configurations et la télémétrie d’utilisation d’un cluster Azure Data Explorer, et propose des recommandations personnalisées et exploitables pour vous aider à optimiser votre cluster.
[Personnaliser les visuels du tableau de bord Azure Data Explorer](dashboard-customize-visuals.md) |Nouvel article. Ce document détaille les différents types de visuels et décrit les différentes options à la disposition des utilisateurs du tableau de bord pour personnaliser leurs visuels.
| [Créer un point de terminaison privé dans votre cluster Azure Data Explorer dans votre réseau virtuel (préversion)](vnet-create-private-endpoint.md) | Article mis à jour. Disponibilité générale.
| [Visualiser des données Azure Data Explorer dans Grafana](grafana.md) | Article mis à jour. Mise à jour avec de nouvelles fonctionnalités.
| [Visualiser des données avec des tableaux de bord Azure Data Explorer](azure-data-explorer-dashboards.md) <br> [Utiliser des paramètres dans des tableaux de bord Azure Data Explorer](dashboard-parameters.md) | Articles mis à jour. Mise à jour avec de nouvelles fonctionnalités.

#### <a name="query"></a>Requête

 Titre de l’article | Description
---|---
[zlib_compress_to_base64_string()](./kusto/query/zlib-base64-compress.md) | Nouvel article.  Effectue la compression zlib et encode le résultat en base64.
[zlib_decompress_from_base64_string()](./kusto/query/zlib-base64-decompress.md) |Nouvel article.  Décode la chaîne d’entrée en base64 et effectue une décompression zlib.
[cosmosdb_sql_request, plug-in](./kusto/query/cosmosdb-plugin.md) | Nouvel article. Le plug-in `cosmosdb_sql_request` envoie une requête SQL à un point de terminaison de réseau SQL Cosmos DB et retourne les résultats de la requête. 
[Fonction materialized_view()](./kusto/query/materialized-view-function.md) |Nouvel article.  Référence la partie matérialisée d’une [vue matérialisée](kusto/management/materialized-views/materialized-view-overview.md). 
| [geo_distance_point_to_line()](./kusto/query/geo-distance-point-to-line-function.md) | Article mis à jour. Ajouter des documents de support multilignes.
|[geo_line_densify()](./kusto/query/geo-line-densify-function.md)| Article mis à jour. Ajouter des documents de support multilignes. |

#### <a name="management"></a>Gestion

 Titre de l’article | Description
---|---
[.create materialized-view](./kusto/management/materialized-views/materialized-view-create.md) | Nouvel article.  Une [vue matérialisée](kusto/management/materialized-views/materialized-view-overview.md) est une requête d’agrégation sur une table source, représentant une seule instruction de synthèse.
[Vues matérialisées (préversion)](./kusto/management/materialized-views/materialized-view-overview.md) | Nouvel article.  Les [vues matérialisées](kusto/query/materialized-view-function.md) exposent une requête d’*agrégation* sur une table source. 
[.alter materialized-view](./kusto/management/materialized-views/materialized-view-alter.md) | Nouvel article. Une modification de la [vue matérialisée](kusto/management/materialized-views/materialized-view-overview.md) peut être utilisée pour modifier la requête d’une vue matérialisée, tout en conservant les données existantes dans la vue.
[.disable .enable materialized-view](./kusto/management/materialized-views/materialized-view-enable-disable.md) | Nouvel article. Décrit comment désactiver une vue matérialisée.
[.drop materialized-view](./kusto/management/materialized-views/materialized-view-drop.md) | Nouvel article. Supprime une vue matérialisée.
[Commandes .show materialized-views](./kusto/management/materialized-views/materialized-view-show-commands.md) | Nouvel article. Les commandes show suivantes affichent des informations sur les [vues matérialisées](kusto/management/materialized-views/materialized-view-overview.md).

#### <a name="functions-library"></a>Bibliothèque de fonctions

 Titre de l’article | Description
---|---
[Bibliothèque de fonctions](./kusto/functions-library/functions-library.md) | Nouvel article. L’article suivant contient une liste par catégorie de [fonctions définies par l’utilisateur (UDF)](kusto/query/functions/user-defined-functions.md). 

#### <a name="api"></a>API

 Titre de l’article | Description
---|---
[SDK Golang Azure Data Explorer](./kusto/api/golang/kusto-golang-client-library.md) | Nouvel article. La bibliothèque de client Go Azure Data Explorer offre la possibilité d’interroger, de contrôler et d’ingérer dans des clusters Azure Data Explorer en utilisant Go. 
[Ingestion sans la bibliothèque Kusto.Ingest](./kusto/api/netfx/kusto-ingest-client-rest.md) | Nouvel article. La bibliothèque Kusto.Ingest est préférable pour l’ingestion de données dans Azure Data Explorer. Vous pouvez cependant obtenir presque les mêmes fonctionnalités, sans être dépendant du package Kusto.Ingest.

## <a name="august-2020"></a>Août 2020

Cette section liste les principales modifications apportées à la documentation en août 2020.

### <a name="general"></a>Général

 Titre de l’article | Description
---|---
[Activer le chiffrement d’infrastructure (double chiffrement) lors de la création de cluster dans Azure Data Explorer](double-encryption.md) | Nouvel article. Si vous avez besoin d’un niveau plus élevé de garantie que vos données sont sécurisées, vous pouvez également activer le [Chiffrement au niveau de l’infrastructure de Stockage Azure](/azure/storage/common/infrastructure-encryption-enable), également appelé double chiffrement.
[Ingérer des données à l’aide du kit de développement logiciel (SDK) Go Azure Data Explorer](go-ingest-data.md) | Nouvel article. Vous pouvez utiliser le [kit de développement logiciel (SDK) Go](https://github.com/Azure/azure-kusto-go) pour ingérer, contrôler et interroger les données des clusters Azure Data Explorer. 
[Vue d’ensemble de la continuité d'activité et de la reprise d'activité](business-continuity-overview.md) | Nouvel article. Dans Azure Data Explorer, la continuité d'activité et la reprise d'activité permettent à votre entreprise de continuer de fonctionner en cas de perturbation. 
[Se connecter à Event Grid](ingest-data-event-grid-overview.md) | Nouvel article. L’ingestion Event Grid est un pipeline qui écoute le stockage Azure et met à jour Azure Data Explorer pour extraire des informations quand des événements associés à des abonnements se produisent. 
[Créer des solutions de continuité d'activité et de reprise d'activité avec Azure Data Explorer](business-continuity-create-solution.md) | Nouvel article. Cet article explique en détail comment vous préparer à une panne régionale Azure en répliquant vos ressources Azure Data Explorer, la gestion et l’ingestion dans différentes régions Azure.

### <a name="query"></a>Requête

 Titre de l’article | Description
---|---
[series_fft()](./kusto/query/series-fft-function.md) | Nouvel article. Applique la transformation de Fourier rapide (FFT) sur une série.  
[series_ifft()](./kusto/query/series-ifft-function.md) | Nouvel article. Applique la transformation de Fourier rapide inverse (IFFT) sur une série. 
[dynamic_to_json()](./kusto/query/dynamic-to-json-function.md) | Nouvel article. Convertit une entrée `dynamic` en représentation sous forme de chaîne. 
[format_ipv4()](./kusto/query/format-ipv4-function.md) | Nouvel article. Analyse l’entrée avec un masque réseau et retourne une chaîne représentant une adresse IPv4.
[format_ipv4_mask()](./kusto/query/format-ipv4-mask-function.md) | Nouvel article. Analyse l’entrée avec un masque réseau et retourne une chaîne représentant une adresse IPv4 dans la notation CIDR.

### <a name="management"></a>Gestion

 Titre de l’article | Description
---|---- 
[Créer ou modifier une exportation continue](./kusto/management/data-export/create-alter-continuous.md) | Nouvel article. Crée ou modifie un travail d’exportation continue.
[Désactiver ou activer une exportation continue](./kusto/management/data-export/disable-enable-continuous.md) | Nouvel article. Désactive ou active le travail d’exportation continue. 
[Supprimer une exportation continue](./kusto/management/data-export/drop-continuous-export.md) | Nouvel article. Supprime un travail d’exportation continue.
[Afficher les artefacts d’une exportation continue](./kusto/management/data-export/show-continuous-artifacts.md) | Nouvel article. Retourne tous les artefacts exportés par l’exportation continue dans toutes les exécutions. Filtrer les résultats selon la colonne Horodatage dans la commande pour afficher uniquement les enregistrements intéressants.
[Afficher une exportation continue](./kusto/management/data-export/show-continuous-export.md) | Nouvel article. Retourne les propriétés de l’exportation continue de *Nom ContinuousExport*. 
[Afficher les échecs d’une exportation continue](./kusto/management/data-export/show-continuous-failures.md) | Nouvel article. Retourne tous les échecs enregistrés dans le cadre de l’exportation continue. Filtrer les résultats selon la colonne Horodatage dans la commande pour afficher seulement les plages horaires intéressantes. 
| [Sécurité au niveau des lignes](kusto/management/rowlevelsecuritypolicy.md) | Article mis à jour. Comment produire une erreur pour un accès non autorisé.
|[Créer et modifier des tables externes dans Stockage Azure ou Azure Data Lake](./kusto/management/external-tables-azurestorage-azuredatalake.md) <br> <br> [Créer et modifier des tables SQL externes](./kusto/management/external-sql-tables.md) | Articles mis à jour. Nouvelle option de commande `.create-or-alter`.

## <a name="next-steps"></a>Étapes suivantes

Pour contribuer à la documentation Azure Data Explorer, consultez le [Guide du contributeur à Docs](https://docs.microsoft.com/contribute/).
