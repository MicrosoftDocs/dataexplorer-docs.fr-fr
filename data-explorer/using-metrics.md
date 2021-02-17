---
title: Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques
description: Découvrez comment utiliser les métriques Azure Data Explorer pour superviser les performances, l’intégrité et l’utilisation du cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/19/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: fb428e443559b579bab4764283ce124f9d9ec192
ms.sourcegitcommit: 62eff65b320ce4ca53eabed6156eb9fe5b77f548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99224211"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques

Les métriques Azure Data Explorer fournissent des indicateurs clés concernant l’intégrité et les performances des ressources du cluster Azure Data Explorer. Utilisez les métriques détaillées dans cet article pour superviser l’utilisation, l’intégrité et les performances du cluster Azure Data Explorer dans votre scénario spécifique en tant que métriques autonomes. Vous pouvez également utiliser des métriques comme base pour les [tableaux de bord Azure](/azure/azure-portal/azure-portal-dashboards) et les [alertes Azure](/azure/azure-monitor/platform/alerts-metric-overview) operationnels.

Pour plus d’informations sur Azure Metrics Explorer, consultez [Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’en avez pas, vous pouvez créer un [compte gratuit Azure](https://azure.microsoft.com/free/).
* Un [cluster et une base de données](create-cluster-database-portal.md).

## <a name="use-metrics-to-monitor-your-azure-data-explorer-resources"></a>Utiliser des métriques pour superviser vos ressources Azure Data Explorer

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
1. Dans le volet gauche de votre cluster Azure Data Explorer, recherchez *métriques*.
1. Sélectionnez **Métriques** pour ouvrir le volet de métriques et commencer l’analyse sur votre cluster.
    :::image type="content" source="media/using-metrics/select-metrics.gif" alt-text="Recherche et sélection de Métriques dans le portail Azure":::

## <a name="work-in-the-metrics-pane"></a>Utiliser le volet Métriques

Dans le volet Métriques, sélectionnez des métriques spécifiques à suivre, choisissez comment agréger vos données et créez des graphiques de métriques à afficher sur votre tableau de bord.

Les sélecteurs **Ressource** et **Espace de noms de métrique**  sont présélectionnés pour votre cluster Azure Data Explorer. Les numéros figurant dans l’image suivante correspondent à la liste numérotée ci-dessous. Ils vous guident dans les différentes options de configuration et d’affichage des métriques.

![Volet Métriques](media/using-metrics/metrics-pane.png)

1. Pour créer un graphique de métriques, sélectionnez le nom de la **Métrique** et l’**agrégation** pertinente par métrique. Pour plus d’informations sur les différentes métriques, consultez [Métriques Azure Data Explorer prises en charge](#supported-azure-data-explorer-metrics).
1. Sélectionnez **Ajouter une métrique** pour afficher le tracé de plusieurs métriques sur le même graphique.
1. Sélectionnez **+ Nouveau graphique** pour afficher plusieurs graphiques dans une même vue.
1. Utilisez le sélecteur de temps pour modifier l’intervalle de temps (par défaut : les dernières 24 heures).
1. Utilisez [**Ajouter un filtre** et **Appliquer la division**](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) pour les métriques qui ont des dimensions.
1. Sélectionnez **Épingler au tableau de bord** pour ajouter la configuration de votre graphique aux tableaux de bord afin de pouvoir le visualiser à nouveau.
1. Définissez **Nouvelle règle d’alerte** pour visualiser vos métriques à l’aide des critères définis. La nouvelle règle d’alerte inclura les dimensions de la ressource, de la métrique, du fractionnement et du filtre cibles de votre graphique. Modifiez ces paramètres dans le [volet de création de règle d’alerte](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

## <a name="supported-azure-data-explorer-metrics"></a>Métriques Azure Data Explorer prises en charge

Les métriques Azure Data Explorer permettent de mieux comprendre les performances globales et l’utilisation de vos ressources, et fournissent des informations sur des actions spécifiques, telles que l’ingestion ou la requête. Les métriques de cet article ont été regroupées par type d’utilisation. 

Les types de métriques sont les suivants : 
* [Métriques de cluster](#cluster-metrics) 
* [Exporter mesures](#export-metrics) 
* [Métriques d’ingestion](#ingestion-metrics) 
* [Métriques d’ingestion de streaming](#streaming-ingest-metrics)
* [Métriques de requête](#query-metrics) 
* [Métriques de vue matérialisée](#materialized-view-metrics)

Pour obtenir la liste alphabétique des métriques d’Azure Monitor pour Azure Data Explorer, consultez [Métriques de cluster Azure Data Explorer prises en charge](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters).

## <a name="cluster-metrics"></a>Métriques de cluster

Les métriques de cluster assurent le suivi de l’intégrité générale du cluster. Par exemple, l’utilisation et la réactivité des ressources et de l’ingestion.

|**Mesure** | **Unité** | **Agrégation** | **Description de la métrique** | **Dimensions** |
|---|---|---|---|---|
| Utilisation du cache | Pourcentage | Moy, Max, Min | Pourcentage des ressources de cache allouées en cours d’utilisation par le cluster. Le cache désigne la taille du disque SSD allouée pour l’activité des utilisateurs conformément à la stratégie de cache définie. <br> <br> Une utilisation moyenne du cache de 80 % ou moins est un état acceptable pour un cluster. Si l’utilisation moyenne du cache est supérieure à 80 %, le cluster doit faire l’objet d’un <br> [scale-up](manage-cluster-vertical-scaling.md) vers un niveau tarifaire optimisé pour le stockage ou d’un <br> [scale-out](manage-cluster-horizontal-scaling.md) vers plus d’instances. Vous pouvez également adapter la stratégie de cache (moins de jours dans le cache). Si l’utilisation du cache est supérieure à 100 %, la taille des données à mettre en cache, conformément à la stratégie de mise en cache, est supérieure à la taille totale du cache sur le cluster. | None |
| UC | Pourcentage | Moy, Max, Min | Pourcentage des ressources de calcul allouées en cours d’utilisation par les ordinateurs du cluster. <br> <br> Une utilisation moyenne de l’UC de 80 % ou moins est acceptable pour un cluster. La valeur maximale d’utilisation de l’UC est de 100 %, ce qui signifie qu’il n’existe aucune ressource de calcul supplémentaire pour traiter les données. <br> Quand les performances d’un cluster ne sont pas satisfaisantes, vérifiez la valeur maximale d’utilisation de l’UC afin de déterminer si des UC spécifiques sont bloquées. | None |
| Utilisation de l’ingestion | Pourcentage | Moy, Max, Min | Pourcentage des ressources réelles utilisées pour ingérer des données par rapport aux ressources totales allouées, dans la stratégie de capacité, pour effectuer l’ingestion. La stratégie de capacité par défaut est la suivante : pas plus de 512 opérations d’ingestion simultanées ou 75 % des ressources de cluster investies dans l’ingestion. <br> <br> Une utilisation moyenne de l’ingestion de 80 % ou moins est un état acceptable pour un cluster. La valeur maximale de l’utilisation d’ingestion est 100 %, ce qui signifie que toute la capacité d’ingestion de cluster est utilisée et qu’une file d’attente d’ingestion risque d’être créée. | None |
| Keep alive | Count | Avg | Effectue le suivi de la réactivité du cluster. <br> <br> Un cluster entièrement réactif retourne la valeur 1, et un cluster bloqué ou déconnecté retourne 0. |
| Nombre total de commandes limitées | Count | Moy, Max, Min, Somme | Nombre de commandes limitées (rejetées) dans le cluster, étant donné que le nombre maximal autorisé de commandes simultanées (parallèles) a été atteint. | None |
| Nombre total d’étendues | Count | Moy, Max, Min, Somme | Nombre total d’étendues de données dans le cluster. <br> <br> Les modifications de cette métrique peuvent impliquer des modifications massives de structures de données et une charge importante sur le cluster, car la fusion d’étendues de données est une activité gourmande en ressources d’UC. | None |

## <a name="export-metrics"></a>Métriques d’exportation

Les métriques d’exportation permettent d’effectuer le suivi de l’intégrité générale et des performances des opérations d’exportation telles que le retard, les résultats, le nombre d’enregistrements et l’utilisation.

|**Mesure** | **Unité** | **Agrégation** | **Description de la métrique** | **Dimensions** |
|---|---|---|---|---|
Exportation continue : Nombre d’enregistrements exportés    | Count | SUM | Nombre d’enregistrements exportés dans tous les travaux d’exportation continue. | ContinuousExportName |
Retard max. pour l’exportation continue |    Count   | Max   | Retard (en minutes) signalé par les travaux d’exportation continue dans le cluster. | None |
Nombre d’exportations continues en attente | Count | Max   | Nombre de travaux d’exportation continue en attente. Ces travaux sont prêts à être exécutés mais attendent dans une file d’attente, probablement en raison d’une capacité insuffisante. 
Résultat de l’exportation continue    | Count |   Count   | Échec ou réussite de chaque exécution d’exportation continue. | ContinuousExportName |
Utilisation de l’exportation |    Pourcentage | Max   | Capacité d’exportation utilisée sur la capacité d’exportation totale du cluster (entre 0 et 100). | None |

## <a name="ingestion-metrics"></a>Métriques d’ingestion

Les métriques d’ingestion permettent d’effectuer le suivi de l’intégrité générale et des performances des opérations d’ingestion telles que la latence, les résultats et le volume.

> [!NOTE]
> * [Appliquer des filtres aux graphiques](/azure/azure-monitor/platform/metrics-charts#apply-filters-to-charts) pour tracer des données partielles par dimensions. Par exemple, explorez l’ingestion dans un `Database` spécifique.
> * [Appliquer un fractionnement à un graphique](/azure/azure-monitor/platform/metrics-charts#apply-splitting-to-a-chart) pour visualiser les données selon différents composants. Ce processus est utile pour analyser les métriques signalées par chaque étape du pipeline d’ingestion, par exemple `Blobs received`.

|**Mesure** | **Unité** | **Agrégation** | **Description de la métrique** | **Dimensions** |
|---|---|---|---|---|
| Nombre d’objets blob du lot  | Count | Moy, Max, Min | Nombre de sources de données d’un lot effectué pour l’ingestion. | Base de données |
| Durée du lot    | Secondes | Moy, Max, Min | Durée de la phase de traitement par lot du flux d’ingestion.  | Base de données |
| Taille du lot        | Octets | Moy, Max, Min | Taille de données attendue non compressée dans un lot agrégé pour l’ingestion. | Base de données |
| Lots traités | Count | Sum, Max, Min | Nombre de lots effectués pour l’ingestion. <br> `Batching Type` : indique si la complétion du lot était basée sur le temps de traitement par lot, la taille des données ou la limite du nombre de fichiers, tel que défini par la [stratégie de traitement par lot](./kusto/management/batchingpolicy.md). | Base de données, type de traitement par lot |
| Objets blob reçus    | Count | Sum, Max, Min | Nombre d’objets blob reçus du flux d’entrée par un composant. <br> <br> Utilisez **Appliquer le fractionnement** pour analyser chaque composant. | Base de données, type de composant, nom du composant |
| Objets blob traités   | Count | Sum, Max, Min | Nombre d’objets blob traités par un composant. <br> <br> Utilisez **Appliquer le fractionnement** pour analyser chaque composant. | Base de données, type de composant, nom du composant |
| Objets blob supprimés     | Count | Sum, Max, Min | Nombre d’objets blob définitivement supprimés par un composant. Pour chacun de ces objets blob, une métrique `Ingestion result` avec un motif d’échec est envoyée. <br> <br> Utilisez **Appliquer le fractionnement** pour analyser chaque composant. | Base de données, type de composant, nom du composant |
| Latence de découverte | Secondes | Avg | Délai entre la mise en file d’attente des données et la découverte par les connexions de données. Ce délai n’est pas inclus dans les métriques **Latence des étapes** ni **Latence d’ingestion** | Type de composant, nom du composant |
| Événements reçus   | Count | Sum, Max, Min | Nombre d’événements reçus par les connexions de données à partir d’un flux d’entrée. | Type de composant, nom du composant |
| Événements traités  | Count | Sum, Max, Min | Nombre d’événements traités par les connexions de données. | Type de composant, nom du composant | 
| Événements supprimés    | Count | Sum, Max, Min | Nombre d’événements définitivement supprimés par les connexions de données. | Type de composant, nom du composant | 
| Événements traités (pour Event/IoT Hubs) | Count | Max, Min, Somme | Nombre total d’événements lus à partir d’Event Hubs et traités par le cluster. Ces événements sont fractionnés en deux groupes : événements rejetés et événements acceptés par le moteur de cluster. | Statut |
| Latence d’ingestion | Secondes | Moy, Max, Min | Latence des données ingérées, depuis la réception des données dans le cluster jusqu’à ce qu’elles soient prêtes à être interrogées. La période de latence d’ingestion varie en fonction du scénario d’ingestion. | None |
| Résultat de l’ingestion  | Count | SUM | Nombre total d’opérations d’ingestion ayant échoué ou réussi. <br> <br> Utilisez **Appliquer le fractionnement** pour créer des compartiments de résultats de réussite et d’échec, et analyser les dimensions (**Valeur** > **État**). <br>Pour plus d’informations sur les résultats d’échec possibles, consultez [Codes d’erreur d’ingestion dans Azure Data Explorer](error-codes.md)| État |
| Volume d’ingestion (en Mo) | Count | Max, Sum | Taille totale des données ingérées dans le cluster (en Mo) avant la compression. | Base de données |
| Longueur de la file d’attente | Count | Avg | Nombre de messages en attente dans la file d’attente d’entrée d’un composant. | Type de composant |
| Message le plus ancien de la file d’attente | Secondes | Avg | Durée en secondes à partir du moment où le message le plus ancien dans la file d’attente d’entrée d’un composant a été inséré. | Type de composant | 
| Taille des données reçues en octets | Octets | Avg, Sum | Taille des données reçues par les connexions de données à partir d’un flux d’entrée. | Type de composant, nom du composant |
| Latence des étapes | Secondes | Avg | Durée à partir du moment où un message est découvert par Azure Data Explorer jusqu’à ce que son contenu soit reçu par un composant d’ingestion pour traitement. <br> <br> Utilisez **Appliquer des filtres** et sélectionnez **Type de composant > EngineStorage** pour afficher la latence d’ingestion totale.| Base de données, type de composant | 

## <a name="streaming-ingest-metrics"></a>Métriques d’ingestion de streaming

Les métriques d’ingestion de streaming effectuent le suivi des données d’ingestion de streaming ainsi que du taux, de la durée et des résultats de requêtes.

|**Mesure** | **Unité** | **Agrégation** | **Description de la métrique** | **Dimensions** |
|---|---|---|---|---|
Taux de données d’ingestion en streaming |    Count   | RateRequestsPerSecond | Volume total de données ingérées dans le cluster. | None |
Durée de l’ingestion en streaming   | Millisecondes  | Moy, Max, Min | Durée totale de toutes les requêtes d’ingestion de streaming. | None |
Taux de demandes d’ingestion en streaming   | Count | Count, Moy, Max, Min, Somme | Nombre total de requêtes d’ingestion de streaming. | None |
Résultat de l’ingestion en streaming | Count | Avg   | Nombre total de requêtes d’ingestion de streaming par type de résultat. | Résultats |

## <a name="query-metrics"></a>Métriques de requête

Les métriques de performances des requêtes effectuent le suivi de la durée des requêtes et du nombre total de requêtes simultanées ou limitées.

|**Mesure** | **Unité** | **Agrégation** | **Description de la métrique** | **Dimensions** |
|---|---|---|---|---|
| Durée de la requête | Millisecondes | Moy, Min, Max, Somme | Durée totale jusqu’à réception des résultats de requête (n’inclut pas la latence du réseau). | QueryStatus |
| Nombre total de demandes simultanées | Count | Moy, Max, Min, Somme | Nombre de requêtes exécutées en parallèle dans le cluster. Cette métrique est un bon moyen d’estimer la charge sur le cluster. | None |
| Nombre total de demandes limitées | Count | Moy, Max, Min, Somme | Nombre de requêtes limitées (rejetées) dans le cluster. Le nombre maximal de requêtes simultanées (parallèles) autorisées est défini dans la stratégie de limites de taux de demandes. | None |

## <a name="materialized-view-metrics"></a>Métriques de vue matérialisée

|**Mesure** | **Unité** | **Agrégation** | **Description de la métrique** | **Dimensions** |
|---|---|---|---|---|
|MaterializedViewHealth                    | 1, 0    | Avg     |  La valeur est 1 si la vue est considérée comme saine, sinon 0. | Base de données, MaterializedViewName |
|MaterializedViewAgeMinutes                | Minutes | Avg     | L’`age` de la vue est défini par l’heure actuelle moins la dernière heure d’ingestion traitée par la vue. La valeur de la métrique est l’heure en minutes (plus la valeur est basse, plus la vue est « saine »). | Base de données, MaterializedViewName |
|MaterializedViewResult                    | 1       | Avg     | La métrique comprend une dimension `Result` indiquant le résultat du dernier cycle de matérialisation (voir les valeurs possibles ci-dessous). La valeur de la métrique est toujours égale à 1. | Base de données, MaterializedViewName, Résultat |
|MaterializedViewRecordsInDelta            | Nombre d’enregistrements | Avg | Nombre d’enregistrements actuellement dans la partie non traitée de la table source. Pour plus d’informations, découvrez [comment fonctionnent les vues matérialisées](./kusto/management/materialized-views/materialized-view-overview.md#how-materialized-views-work)| Base de données, MaterializedViewName |
|MaterializedViewExtentsRebuild            | Nombre d’étendues | Avg | Nombre d’étendues recréées dans le cycle de matérialisation. | Base de données, MaterializedViewName|
|MaterializedViewDataLoss                  | 1       | Max    | La métrique est déclenchée lorsque les données sources non traitées s’approchent de la conservation. | Base de données, MaterializedViewName, Genre |

## <a name="next-steps"></a>Étapes suivantes

* [Tutoriel : Ingérer et interroger des données de supervision dans Azure Data Explorer](ingest-data-no-code.md)
* [Superviser les opérations d’ingestion d’Azure Data Explorer à l’aide des journaux de diagnostic](using-diagnostic-logs.md)
* [Démarrage rapide : Interroger des données dans l’Explorateur de données Azure](web-query-data.md)
