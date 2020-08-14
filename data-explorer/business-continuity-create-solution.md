---
title: Créer des solutions de continuité d'activité et de reprise d'activité avec Azure Data Explorer
description: Cet article explique comment créer des solutions de continuité d'activité et de reprise d'activité avec Azure Data Explorer
author: orspod
ms.author: orspodek
ms.reviewer: herauch
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: 0b37d65d095806d108a632c315820d164f45a520
ms.sourcegitcommit: 39a055e246539ac64d651abb42531892dd4393e7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88028890"
---
# <a name="create-business-continuity-and-disaster-recovery-solutions-with-azure-data-explorer"></a>Créer des solutions de continuité d'activité et de reprise d'activité avec Azure Data Explorer

Cet article explique en détail comment vous préparer à une panne régionale Azure en répliquant vos ressources Azure Data Explorer, la gestion et l’ingestion dans différentes régions Azure. Un exemple d’ingestion de données avec Event Hub est fourni. L’optimisation des coûts y est également abordée pour différentes configurations architecturales. Pour une analyse plus approfondie des considérations d’architecture et des solutions de reprise d’activité, consultez la [vue d’ensemble relative à la continuité d’activité](business-continuity-overview.md).

## <a name="prepare-for-azure-regional-outage-to-protect-your-data"></a>Se préparer à une panne régionale Azure pour protéger ses données

Azure Data Explorer ne prend pas en charge la protection automatique en cas de panne d’une région Azure entière. Cette interruption peut se produire lors d’une catastrophe naturelle, telle qu’un tremblement de terre. S’il vous faut une solution de récupération d’urgence, procédez comme suit afin de garantir la continuité d’activité. Au cours de ces étapes, vous allez répliquer vos clusters, la gestion et l’ingestion de données dans deux régions jumelées Azure.

1. [Créez un minimum de clusters indépendants](#create-multiple-independent-clusters) dans deux régions jumelées Azure.
1. [Répliquez toutes les activités de gestion](#replicate-management-activities), telles que la création des tables ou la gestion des rôles d’utilisateur sur chaque cluster.
1. Ingérez des données dans chaque cluster en parallèle.

### <a name="create-multiple-independent-clusters"></a>Créer plusieurs clusters indépendants

Créez plusieurs [clusters Azure Data Explorer](create-cluster-database-portal.md) dans différentes régions.
Veillez à ce qu’au moins deux de ces clusters soient créés dans des [régions jumelées Azure](/azure/best-practices-availability-paired-regions).

L’illustration suivante montre les réplicas, trois clusters dans trois régions différentes. 

:::image type="content" source="media/business-continuity-create-solution/independent-clusters.png" alt-text="Créer des clusters indépendants":::

### <a name="replicate-management-activities"></a>Répliquer des activités de gestion

Répliquez les activités de gestion de manière à disposer de la même configuration de cluster dans chaque réplica.

1. Sur chaque réplica, créez les mêmes : 
    * Bases de données : Vous pouvez utiliser le [portail Azure](create-cluster-database-portal.md#create-a-database) ou l’un de nos [kits de développement logiciel (SDK)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/kusto/Microsoft.Azure.Management.Kusto) pour créer une nouvelle base de données.
    * [Tables](kusto/management/create-table-command.md)
    * [Mappages](kusto/management/create-ingestion-mapping-command.md)
    * [Stratégies](kusto/management/policies.md)

1. Gérez [l’authentification et l’autorisation](kusto/management/security-roles.md) sur chaque réplica.

    :::image type="content" source="media/business-continuity-create-solution/regional-duplicate-management.png" alt-text="Dupliquer des activités de gestion":::    

### <a name="configure-data-ingestion"></a>Configurer l’ingestion des données

Configurez l’ingestion des données de manière cohérente sur chaque cluster. Les méthodes d’ingestion suivantes utilisent les fonctionnalités de continuité d’activité avancées suivantes.

|Méthode d’ingestion  |Fonctionnalité de récupération d’urgence  |
|---------|---------|
|[Iot Hub](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr)  |[Basculement initié par Microsoft et basculement manuel](/azure/iot-hub/iot-hub-ha-dr#cross-region-dr) |
|[Concentrateur d’événements](ingest-data-event-hub.md) | Récupération d’urgence des métadonnées à l’aide d'[espaces de noms de récupération d’urgence principal et secondaire](/azure/event-hubs/event-hubs-geo-dr)     |
|[Ingérer à partir du stockage avec un abonnement Event Grid](ingest-data-event-grid.md) |  Implémenter une [géo-reprise d'activité après sinistre](/azure/event-hubs/event-hubs-geo-dr) pour les messages créés à partir d’objets blob envoyés à Event Hub et à la [stratégie de récupération d’urgence et de basculement de compte](/azure/storage/common/storage-disaster-recovery-guidance)       |

## <a name="disaster-recovery-solution-using-event-hub-ingestion"></a>Solution de récupération d’urgence utilisant l’ingestion d’Event Hub

Après vous être [préparé à une panne régionale Azure pour protéger vos données](#prepare-for-azure-regional-outage-to-protect-your-data), ces dernières, de même que la gestion sont distribuées dans plusieurs régions. En cas de panne dans une région, Azure Data Explorer est en mesure d’utiliser d’autres réplicas. 

### <a name="set-up-ingestion-using-event-hub"></a>Configurer l’ingestion à l’aide d’Event Hub

L’exemple suivant utilise l’ingestion via Event Hub. Un [flux de basculement](/azure/event-hubs/event-hubs-geo-dr#setup-and-failover-flow) a été configuré et Azure Data Explorer ingère les données à partir de l’alias. [Ingérez les données à partir d’Event Hub](ingest-data-event-hub.md) à l’aide d’un groupe de consommateurs unique par réplica de cluster. À défaut, vous risquez de distribuer le trafic plutôt que de le répliquer.

> [!NOTE] 
> L’ingestion via Event Hub/IoT Hub/Stockage est robuste. Si un cluster n’est pas disponible pendant un certain temps, il rattrapera son retard ultérieurement et insérera les messages ou objets blob en attente. Ce processus s’appuie sur les [points de contrôle](/azure/event-hubs/event-hubs-features#checkpointing).

:::image type="content" source="media/business-continuity-create-solution/event-hub-management-scheme.png" alt-text="Ingestion via Event Hub":::

Comme illustré dans le diagramme ci-dessous, vos sources de données produisent des événements dans l’instance Event Hub configurée pour le basculement, et chaque réplica Azure Data Explorer consomme les événements. Les composants de visualisation des données tels que Power BI, Grafana ou les applications web alimentées par les kits de développement logiciel (SDK) peuvent interroger l’un des réplicas.

:::image type="content" source="media/business-continuity-create-solution/data-sources-visualization.png" alt-text="Sources de données pour la visualisation de données":::

## <a name="optimize-costs"></a>Optimiser les coûts

Vous êtes maintenant prêt à optimiser vos réplicas à l’aide des méthodes suivantes :

* [Créer une configuration de secours active](#create-an-active-hot-standby-configuration)
* [Démarrer et arrêter les réplicas](#start-and-stop-the-replicas)
* [Implémenter un service d’application hautement disponible](#implement-a-highly-available-application-service)
* [Optimiser les coûts dans une configuration active-active](#optimize-cost-in-an-active-active-configuration)

### <a name="create-an-active-hot-standby-configuration"></a>Créer une configuration de secours active

La réplication et la mise à jour de la configuration Azure Data Explorer augmentent de manière linéaire le coût en fonction du nombre de réplicas. A des fins d’optimisation des coûts, vous pouvez implémenter une variante architecturale pour équilibrer le temps, le basculement et le coût.
Dans une configuration de secours active, l’optimisation des coûts est implémentée moyennant l’introduction de réplicas Azure Data Explorer passifs. Ces réplicas ne sont activés qu’en cas d’incident dans la région primaire (région A, par exemple). Les réplicas des régions B et C n’ont pas à être actifs 24 heures sur 24, 7 jours sur 7, ce qui réduit considérablement les coûts. Dans la plupart des cas cependant, les performances de ces réplicas ne sont pas aussi bonnes que celles du cluster principal. Pour plus d’informations, consultez [Configuration de secours active](business-continuity-overview.md#active-hot-standby-configuration).

Dans l’illustration ci-dessous, un seul cluster ingère des données à partir d’Event Hub. Le cluster principal de la région A se charge de l’[exportation continue](kusto/management/data-export/continuous-data-export.md) de toutes les données vers un compte de stockage. Les réplicas secondaires accèdent aux données à l’aide de [tables externes](kusto/query/schema-entities/externaltables.md).

:::image type="content" source="media/business-continuity-create-solution/active-hot-standby-scheme.png" alt-text="architecture pour un cluster de secours actif":::

### <a name="start-and-stop-the-replicas"></a>Démarrer et arrêter les réplicas 

Vous pouvez démarrer et arrêter les réplicas secondaires à l’aide d’une des méthodes suivantes :

* [Connecteur Azure Data Explorer vers Power Automate (préversion)](flow.md)

* Bouton **Arrêter** sous l’onglet **Vue d’ensemble** dans le portail Azure. Pour plus d’informations, consultez [Arrêter et redémarrer le cluster](create-cluster-database-portal.md#stop-and-restart-the-cluster).

* Azure CLI : 

```kusto
az kusto cluster stop --name=<clusterName> --resource-group=<rgName> --subscription=<subscriptionId>” 
```

### <a name="implement-a-highly-available-application-service"></a>Implémenter un service d’application hautement disponible

#### <a name="create-the-azure-app-service-bcdr-client"></a>Créer le client BCDR Azure App Service

Cette section explique comment créer une instance [Azure App Service](https://azure.microsoft.com/services/app-service/) prenant en charge une connexion à un cluster Azure Data Explorer principal et plusieurs clusters secondaires. L’image suivante illustre la configuration d’Azure App Service.

:::image type="content" source="media/business-continuity-create-solution/app-service-setup.png" alt-text="Créez un plan Azure App Service":::

> [!TIP]
> Le fait de disposer de plusieurs connexions entre les réplicas du même service offre une disponibilité accrue. Cette configuration n’est pas seulement utile dans les cas de pannes régionales.  

1. Utilisez ce [code réutilisable pour une instance App Service](https://github.com/Azure/azure-kusto-bcdr-boilerplate). La classe [AdxBcdrClient](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/master/webapp/ADX/AdxBcdrClient.cs) a été créée pour implémenter un client à plusieurs clusters. Dans un premier temps, chaque requête exécutée à l’aide de ce client est envoyée [au cluster principal](https://github.com/Azure/azure-kusto-bcdr-boilerplate/blob/26f8c092982cb8a3757761217627c0e94928ee07/webapp/ADX/AdxBcdrClient.cs#L69). En cas d’échec, la requête est envoyée aux réplicas secondaires.

1. Utilisez des [métriques d’insights d’application personnalisées](/azure/azure-monitor/app/api-custom-events-metrics) pour mesurer les performances et demander une distribution vers les clusters principaux et secondaires. 

#### <a name="test-the-azure-app-service-bcdr-client"></a>Tester le client BCDR Azure App Service

Nous avons exécuté un test à l’aide de plusieurs réplicas Azure Data Explorer. Après une panne simulée de clusters principaux et secondaires, vous pouvez voir que le client BCDR App Service se comporte comme prévu.

:::image type="content" source="media/business-continuity-create-solution/simulation-verify-service.png" alt-text="Vérifier le client BCDR App service":::

Les clusters Azure Data Explorer sont répartis dans les régions Europe Ouest (2xD14v2 primaire), Asie Sud-Est et USA Est (2xD11v2). 

:::image type="content" source="media/business-continuity-create-solution/performance-test-query-time.png" alt-text="Temps de réponse des requêtes mondiales":::

> [!NOTE]
> Les temps de réponse plus lents sont dus à des références SKU et des requêtes mondiales.

#### <a name="perform-dynamic-or-static-routing"></a>Effectuer un routage dynamique ou statique

Utilisez les [méthodes de routage Azure Traffic Manager](/azure/traffic-manager/traffic-manager-routing-methods) à des fins de routage dynamique ou statique des requêtes.  L’équilibreur de charge de trafic DNS Azure Traffic Manager vous permet de répartir le trafic App Service. Ce trafic est optimisé pour les services des régions Azure du monde entier, tout en offrant une disponibilité et une réactivité élevées 

Vous pouvez également utiliser le [routage basé sur Azure Front Door](/azure/frontdoor/front-door-routing-methods). Pour une comparaison de ces deux méthodes, consultez [Équilibrage de charge avec la suite de livraison d’application Azure](/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).

### <a name="optimize-cost-in-an-active-active-configuration"></a>Optimiser les coûts dans une configuration active-active

L’utilisation d’une configuration active-active à des fins de récupération d’urgence augmente le coût de façon linéaire. Le coût comprend les nœuds, le stockage, le balisage et la hausse du coût de mise en réseau pour la [bande passante](https://azure.microsoft.com/pricing/details/bandwidth/).

#### <a name="use-optimized-autoscale-to-optimize-costs"></a>Utiliser la mise à l’échelle automatique optimisée pour optimiser les coûts

Utilisez la fonctionnalité de [mise à l’échelle automatique optimisée](manage-cluster-horizontal-scaling.md#optimized-autoscale) pour configurer la mise à l’échelle horizontale des clusters secondaires. Ils doivent être dimensionnés de manière à gérer la charge d’ingestion. Lorsque le cluster principal n’est pas accessible, les clusters secondaires reçoivent plus de trafic et sont mis à l’échelle en fonction de la configuration. 

Dans cet exemple, l’utilisation de la mise à l’échelle automatique optimisée a permis d’économiser environ 50 % du coût par rapport à la même échelle horizontale et verticale sur tous les réplicas.

## <a name="next-steps"></a>Étapes suivantes

Utiliser la [vue d’ensemble de la continuité d'activité et de la reprise d'activité](business-continuity-overview.md).
