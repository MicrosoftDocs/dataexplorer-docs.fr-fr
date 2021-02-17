---
title: Optimiser pour une haute simultanéité avec Azure Data Explorer
description: Dans cet article, vous allez apprendre à optimiser votre configuration Azure Data Explorer pour une haute simultanéité.
author: orspod
ms.author: orspodek
ms.reviewer: miwalia
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: 2fd45c2608e83c46b39d65fd8d3d564ca1b2df29
ms.sourcegitcommit: 2b0156fc244757e62aaef5d4782c4abebaf09754
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/08/2021
ms.locfileid: "99808684"
---
# <a name="optimize-for-high-concurrency-with-azure-data-explorer"></a>Optimiser pour une haute simultanéité avec Azure Data Explorer

Des applications hautement simultanées sont nécessaires dans les scénarios avec une base d’utilisateurs volumineuse, où l’application gère simultanément de nombreuses demandes avec une faible latence et un débit élevé. 

Les cas d’utilisation incluent des tableaux de bord de supervision et d’alerte à grande échelle, par exemple des produits et services Microsoft comme [Azure Monitor](https://azure.microsoft.com/services/monitor/), [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) et [PlayFab](https://playfab.com/). Tous ces services utilisent Azure Data Explorer pour le traitement des charges de travail hautement simultanées. Azure Data Explorer est un service d’analytique Big Data rapide et complètement managé pour l’analytique en temps réel d’importants volumes de données diffusées en continu à partir d’applications, de sites web, d’appareils IoT, etc. 

> [!NOTE]
> Le nombre réel de requêtes pouvant s’exécuter simultanément sur un cluster dépend de divers facteurs, tels que les ressources de cluster, les volumes de données, la complexité des requêtes et les modèles d’utilisation.

Pour la configuration des applications hautement simultanées, concevez l’architecture back-end comme suit :

* [Optimiser les données](#optimize-data)
* [Définir le modèle d’architecture responsable-abonné](#set-leader-follower-architecture-pattern)
* [Optimiser les requêtes](#optimize-queries)
* [Définir des stratégies de cluster](#set-cluster-policies)
* [Superviser les clusters Azure Data Explorer](#monitor-azure-data-explorer-clusters)

Cet article présente différentes recommandations pour chacun des sujets ci-dessus que vous pouvez implémenter pour obtenir une haute simultanéité de manière optimale et rentable. Ces fonctionnalités peuvent être utilisées seules ou en les associant.


## <a name="optimize-data"></a>Optimiser les données

Pour une haute simultanéité, les requêtes doivent consommer le moins de ressources processeur possible. Vous pouvez utiliser l’une ou l’ensemble des méthodes suivantes : [conception de schéma de table optimisée](#use-table-schema-design-best-practices), [partitionnement des données](#partition-data), [pré-agrégation](#pre-aggregate-your-data-with-materialized-views) et [mise en cache](#configure-caching-policy).

### <a name="use-table-schema-design-best-practices"></a>Utiliser les bonnes pratiques de conception de schéma de table

Utilisez les suggestions de conception de schéma de table suivantes pour réduire au minimum les ressources processeur utilisées :

* Faites correspondre le type de données des colonnes de manière optimale aux données réelles stockées dans ces colonnes. Par exemple, ne stockez pas de valeurs datetime dans une colonne de chaîne.
* Évitez les grandes tables éparses avec de nombreuses colonnes et l’utilisation de colonnes dynamiques pour stocker des propriétés éparses.
* Stockez les propriétés fréquemment utilisées dans leur propre colonne avec un type de données non dynamique.
* Dénormalisez les données pour éviter les jointures nécessitant des ressources processeur relativement volumineuses.

### <a name="partition-data"></a>Données de partition

Les données sont stockées sous la forme d’extensions (partitions de données) et sont partitionnées par heure d’ingestion par défaut. La [stratégie de partitionnement](kusto/management/partitioningpolicy.md) vous permet de repartitionner les extensions en fonction d’une colonne de chaîne unique ou d’une colonne datetime unique dans un processus en arrière-plan. Le partitionnement peut améliorer considérablement les performances lorsque la plupart des requêtes utilisent des clés de partition pour filtrer, agréger, ou les deux.

> [!NOTE]
> Le processus de partitionnement lui-même utilise des ressources processeur. Toutefois, la réduction de processeur pendant la durée de la requête doit l’emporter sur le processeur utilisé pour le partitionnement.

### <a name="pre-aggregate-your-data-with-materialized-views"></a>Pré-agréger vos données avec des vues matérialisées

Pré-agrégez vos données pour réduire considérablement les ressources processeur pendant la durée de la requête. Les exemples de scénarios incluent la synthèse des points de données sur un nombre réduit de compartiments de temps, la conservation du dernier enregistrement d’un enregistrement donné ou la déduplication du jeu de données. Utilisez des [vues matérialisées](kusto/management/materialized-views/materialized-view-overview.md) pour obtenir une vue agrégée facile à configurer sur les tables sources. Cette fonctionnalité simplifie l’effort de création et de gestion de ces vues agrégées.

> [!NOTE]
> Le processus d’agrégation en arrière-plan utilise des ressources processeur. Toutefois, la réduction de processeur pendant la durée de la requête doit l’emporter sur la consommation de processeur pour l’agrégation.

### <a name="configure-caching-policy"></a>Configurer la stratégie de mise en cache

Configurez la stratégie de mise en cache pour que les requêtes s’exécutent sur des données stockées dans le stockage chaud, également appelé cache de disque. Exécutez uniquement des scénarios limités et soigneusement conçus sur le stockage froid ou sur des tables externes.

## <a name="set-leader-follower-architecture-pattern"></a>Définir le modèle d’architecture responsable-abonné

La base de données d’abonné est une fonctionnalité qui suit une base de données ou un ensemble de tables dans une base de données à partir d’un autre cluster situé dans la même région. Cette fonctionnalité est exposée via [Azure Data Share](/azure/data-explorer/data-share), les [API Azure Resource Manager](follower.md) et un ensemble de [commandes de cluster](kusto/management/cluster-follower.md). 

Utilisez le modèle responsable-abonné pour définir des ressources de calcul pour différentes charges de travail. Par exemple, configurez un cluster pour les ingestions, un cluster pour l’interrogation ou le traitement des tableaux de bord ou des applications, et un cluster qui traite les charges de travail de science des données. Dans ce cas, chaque charge de travail aura des ressources de calcul dédiées qui peuvent être mises à l’échelle de manière indépendante ainsi que des configurations de sécurité et de mise en cache différentes. Tous les clusters utilisent les mêmes données, avec le responsable qui écrit les données, et les abonnés qui les utilisent en mode lecture seule.

> [!NOTE]
> Les bases de données d’abonnés ont un décalage par rapport au responsable, généralement quelques minutes. Si votre solution requiert les données les plus récentes sans latence, la solution suivante peut être utile : Utilisez une vue sur le cluster d’abonné qui réunit les données du responsable et de l’abonné, en interrogeant les données les plus récentes à partir du responsable et le reste des données à partir de l’abonné.

Pour améliorer les performances des requêtes sur le cluster d’abonné, vous pouvez activer la [configuration de prefetch-extents](kusto/management/cluster-follower.md#alter-follower-database-prefetch-extents). Toutefois, utilisez cette configuration avec précaution, car elle peut avoir un impact sur l’actualisation des données dans la base de données d’abonné.

## <a name="optimize-queries"></a>Optimiser les requêtes

Utilisez les méthodes suivantes afin d’optimiser vos requêtes pour une haute simultanéité.

### <a name="use-query-results-cache"></a>Utiliser le cache des résultats de la requête

Quand plusieurs utilisateurs chargent le même tableau de bord au même moment, le tableau de bord pour le deuxième utilisateur et les suivants peut être fourni à partir du cache. Cette configuration offre des performances élevées avec presque pas d’utilisation du processeur. Utilisez la fonctionnalité du [cache des résultats de la requête](kusto/query/query-results-cache.md) et envoyez la configuration du cache des résultats de la requête avec la requête à l’aide de l’instruction `set`.

[Grafana](/azure/data-explorer/grafana) contient un paramètre de configuration pour le cache des résultats de la requête au niveau de la source de données. Par conséquent, tous les tableaux de bord utilisent ce paramètre par défaut et n’ont pas besoin de modifier la requête.

### <a name="configure-query-consistency"></a>Configurer la cohérence des requêtes

Il existe deux modèles de cohérence des requêtes : *forte* (par défaut) et *faible*. Avec une cohérence forte, seul un état de données cohérent à jour est visible, quel que soit le nœud de calcul qui reçoit la requête. Avec une cohérence faible, les nœuds actualisent régulièrement leur copie des métadonnées, ce qui aboutit à une latence de 1 à 2 minutes dans la synchronisation des modifications de métadonnées. Le modèle faible vous permet de réduire la charge sur le nœud qui gère les modifications des métadonnées, ce qui assure une simultanéité plus haute que la cohérence forte par défaut. Définissez cette configuration dans les [propriétés de demande de client](kusto/api/netfx/request-properties.md) et dans les configurations de source de données Grafana.

> [!NOTE]
> Si vous avez besoin de réduire encore davantage le décalage pour l’actualisation des métadonnées, ouvrez un ticket de support.

### <a name="optimize-queries"></a>Optimiser les requêtes

Suivez les [bonnes pratiques relatives aux requêtes](kusto/query/best-practices.md) afin que vos requêtes soient aussi efficaces que possible.

## <a name="set-cluster-policies"></a>Définir des stratégies de cluster

Le nombre de requêtes simultanées est limité par défaut et contrôlé par la [stratégie de limites de taux de demandes](kusto/management/request-rate-limit-policy.md) afin que le cluster ne soit pas surchargé. Vous pouvez ajuster cette stratégie pour les situations de haute simultanéité, mais uniquement après des tests rigoureux, de préférence sur des jeux de données et modèles de requête de type production. Les tests garantissent que le cluster peut supporter la valeur modifiée. Cette limite peut être configurée en fonction des besoins de l’application.

## <a name="monitor-azure-data-explorer-clusters"></a>Superviser les clusters Azure Data Explorer

La supervision de l’intégrité de vos ressources de cluster vous aide à créer un plan d’optimisation à l’aide des fonctionnalités suggérées dans les sections ci-dessus. Azure Monitor pour Azure Data Explorer offre une vue complète des performances, des opérations, de l’utilisation et des défaillances de votre cluster. Pour obtenir des insights sur les performances des requêtes, les requêtes simultanées, les requêtes limitées et d’autres métriques, cliquez sur l’onglet **Insights (préversion)** sous la section de supervision du cluster Azure Data Explorer dans le portail Azure. Pour plus d’informations sur la supervision des clusters, reportez-vous à la documentation [Azure Monitor pour Azure Data Explorer](/azure/azure-monitor/insights/data-explorer?toc=/azure/data-explorer/toc.json&amp;bc=/azure/data-explorer/breadcrumb/toc.json). Pour plus d’informations sur les métriques individuelles, consultez [Métriques Azure Data Explorer](using-metrics.md#supported-azure-data-explorer-metrics).
