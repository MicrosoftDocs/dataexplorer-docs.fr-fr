---
title: Utiliser les recommandations d’Azure Advisor pour optimiser votre cluster Azure Data Explorer
description: Cet article décrit les recommandations d’Azure Advisor utilisées pour optimiser votre cluster Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: lizlotor
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/14/2020
ms.openlocfilehash: 1d7fafcab3293a66bafb4b60f86413d00ee9c354
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241888"
---
# <a name="use-azure-advisor-recommendations-to-optimize-your-azure-data-explorer-cluster-preview"></a>Utiliser les recommandations d’Azure Advisor pour optimiser votre cluster Azure Data Explorer (préversion)

Azure Advisor analyse les configurations et la télémétrie d’utilisation d’un cluster Azure Data Explorer, et propose des recommandations personnalisées et exploitables pour vous aider à optimiser votre cluster.

## <a name="access-the-azure-advisor-recommendations"></a>Vous disposez de recommandations d’Azure Advisor

Il existe deux façons d’accéder aux recommandations d’Azure Advisor.

### <a name="view-azure-advisor-recommendations-for-your-azure-data-explorer-cluster"></a>Visualiser les recommandations d’Azure Advisor pour votre cluster Azure Data Explorer

1. Dans le portail Azure, accédez à la page de votre cluster Azure Data Explorer. 
1. Dans le menu de gauche, sous **Supervision** , sélectionnez **Recommandations du conseiller** . Une liste de recommandations s’ouvre pour ce cluster.

    :::image type="content" source="media/azure-advisor/resource-group-advisor-recommendations.png" alt-text="Recommandations d’Azure Advisor pour votre cluster Azure Data Explorer"::: 

### <a name="view-azure-advisor-recommendations-for-all-clusters-in-your-subscription"></a>Visualiser les recommandations d’Azure Advisor pour tous les clusters de votre abonnement

1. Dans le portail Azure, accédez à la page [Ressource Advisor](https://ms.portal.azure.com/#blade/Microsoft_Azure_Expert/AdvisorMenuBlade/overview). 
1. Dans **Vue d’ensemble** , sélectionnez le ou les abonnements pour lesquels vous souhaitez obtenir des recommandations. 
1. Sélectionnez **Clusters Azure Data Explorer** et **Bases de données Azure Data Explorer** dans la deuxième liste déroulante.
 
    :::image type="content" source="media/azure-advisor/advisor-resource.png" alt-text="Recommandations d’Azure Advisor pour votre cluster Azure Data Explorer":::

## <a name="use-the-azure-advisor-recommendations"></a>Utiliser les recommandations d’Azure Advisor

Il existe plusieurs types de recommandations d’Azure Advisor. Utilisez le type de recommandation approprié pour optimiser votre cluster. 

1. Dans **Advisor** , sous **Recommandations** , sélectionnez **Coût** pour les recommandations relatives au coût.

    :::image type="content" source="media/azure-advisor/select-recommendation-type.png" alt-text="Recommandations d’Azure Advisor pour votre cluster Azure Data Explorer":::

1. Sélectionnez une recommandation dans la liste. 

    :::image type="content" source="media/azure-advisor/select-recommendation.png" alt-text="Recommandations d’Azure Advisor pour votre cluster Azure Data Explorer":::

1. La fenêtre suivante contient la liste des clusters pour lesquels la recommandation est pertinente. Les détails de la recommandation sont différents pour chaque cluster et incluent l’action recommandée.

    :::image type="content" source="media/azure-advisor/clusters-with-recommendations.png" alt-text="Recommandations d’Azure Advisor pour votre cluster Azure Data Explorer":::

## <a name="recommendation-types"></a>Types de recommandation

Des recommandations sur le coût et les performances sont actuellement disponibles.

> [!IMPORTANT]
> Vos économies annuelles réelles peuvent varier. Les économies annuelles présentées sont basées sur les tarifs du paiement à l’utilisation. L’économie potentielle ne prend pas en compte les remises liées à la facturation des instances de machine virtuelle réservée Azure.

### <a name="cost-recommendations"></a>Recommandations en matière de coûts

Les recommandations relatives au **coût** sont disponibles pour les clusters qui peuvent être modifiés pour réduire les coûts sans compromettre les performances. Les recommandations relatives aux coûts sont les suivantes : 

* [Cluster Azure Data Explorer inutilisé](#azure-data-explorer-unused-cluster)
* [Clusters Azure Data Explorer contenant des données avec une faible activité](#azure-data-explorer-clusters-containing-data-with-low-activity)
* [Dimensionner correctement un cluster Azure Data Explorer pour optimiser les coûts](#correctly-size-azure-data-explorer-clusters-to-optimize-cost)
* [Réduire la période de mise en cache des tables Azure Data Explorer](#reduce-cache-for-azure-data-explorer-tables)

#### <a name="azure-data-explorer-unused-cluster"></a>Cluster Azure Data Explorer inutilisé

Un cluster est considéré comme inutilisé s’il a une petite quantité de données, de requêtes et d’événements d’ingestion au cours des 30 derniers jours, une faible utilisation du processeur au cours des deux derniers jours et aucun abonné au cours de la journée précédente. La recommandation **Envisagez de supprimer les clusters vides / inutilisés** a comme action recommandée de supprimer le cluster inutilisé.

#### <a name="azure-data-explorer-clusters-containing-data-with-low-activity"></a>Clusters Azure Data Explorer contenant des données avec une faible activité

La recommandation **Arrêter les clusters Azure Data Explorer pour réduire les coûts et conserver leurs données** est donnée pour un cluster qui contient des données mais qui a une activité faible. Une faible activité se traduit par un petit nombre de requêtes et d’ingestion au cours des 30 derniers jours, une faible utilisation du processeur au cours des deux derniers jours et aucun abonné au cours de la journée précédente. Il est recommandé d’arrêter le cluster afin de réduire les coûts mais tout en conservant les données. Si les données ne sont pas nécessaires, supprimez le cluster pour optimiser vos économies.

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-cost"></a>Dimensionner correctement les clusters Azure Data Explorer pour optimiser les coûts

La recommandation **Dimensionnez correctement les clusters Azure Data Explorer pour un coût optimal** est donnée pour un cluster dont la taille ou la référence SKU de machine virtuelle n’est pas optimisée pour les coûts. Cette recommandation est basée sur des paramètres comme sa capacité de données, et l’utilisation du processeur et de l’ingestion au cours de la semaine précédente. Vous pouvez réduire les coûts en redimensionnant la configuration du cluster recommandée en utilisant le [scale-down](manage-cluster-vertical-scaling.md) et le [scale-in](manage-cluster-horizontal-scaling.md).

Il est recommandé d’utiliser une [Configuration de mise à l’échelle automatique](manage-cluster-horizontal-scaling.md#optimized-autoscale). Si vous utilisez la mise à l’échelle automatique optimisée et que vous voyez la recommandation de taille sur votre cluster, c’est que votre référence SKU de machine virtuelle actuelle ou les limites du nombre minimal et maximal d’instances de la mise à l’échelle automatique optimisée ne sont pas optimales. Le nombre d’instances recommandé doit être inclus dans les limites que vous avez définies. Pour plus d’informations, consultez les [références (SKU) des machines virtuelles](manage-cluster-choose-sku.md) et les [tarifs](https://azure.microsoft.com/pricing/details/data-explorer/).

> [!TIP]
> La configuration de la mise à l’échelle automatique optimisée ne modifie pas immédiatement le nombre d’instances. Pour des modifications instantanées, utilisez la [mise à l’échelle manuelle](manage-cluster-horizontal-scaling.md#manual-scale) pour réinitialiser le nombre d’instances recommandé, puis activez la mise à l’échelle automatique optimisée pour permettre les optimisations futures.

#### <a name="reduce-cache-for-azure-data-explorer-tables"></a>Réduire la période de mise en cache des tables Azure Data Explorer

La recommandation **Réduire la période de cache des tables Azure Data Explorer pour l’optimisation des coûts du cluster** est donnée pour un cluster qui peut réduire la [stratégie de cache](kusto/management/cachepolicy.md) de sa table. Cette recommandation est basée sur la période de recherche dans l’antériorité des requêtes au cours des 30 derniers jours. Vous voyez les 10 premières tables avec des économies potentielles dans le cache. Cette recommandation est proposée seulement si le cluster peut effectuer un scale-in ou un scale-down à la suite de la modification de la stratégie de cache. Advisor vérifie si le cluster est « délimité par les données », ce qui signifie que le cluster a une faible utilisation du processeur et une faible ingestion, mais qu’en raison d’une capacité de données élevée, le cluster ne peut pas faire l’objet d’un scale-in ou d’un scale-down.

### <a name="performance-recommendations"></a>Recommandations en matière de performances

Les recommandations liées aux **performances** permettent d’améliorer les performances de vos clusters Azure Data Explorer. Les recommandations relatives aux performances sont les suivantes : 
* [Dimensionner correctement un cluster Azure Data Explorer pour optimiser les performances](#correctly-size-azure-data-explorer-clusters-to-optimize-performance)
* [Mettre à jour la stratégie de cache pour les tables Azure Data Explorer](#update-cache-policy-for-azure-data-explorer-tables)

#### <a name="correctly-size-azure-data-explorer-clusters-to-optimize-performance"></a>Dimensionner correctement un cluster Azure Data Explorer pour optimiser les performances

La recommandation **Dimensionnez correctement les clusters Azure Data Explorer pour des performances optimales** est donnée pour un cluster dont la taille ou la référence SKU de machine virtuelle n’est pas optimisée pour les performances. Cette recommandation est basée sur des paramètres comme sa capacité de données, et l’utilisation du processeur et de l’ingestion au cours de la semaine précédente. Vous pouvez améliorer les performances en redimensionnant correctement la configuration de cluster recommandée en utilisant le [scale-up](manage-cluster-vertical-scaling.md) et le [scale-out](manage-cluster-horizontal-scaling.md).

Il est recommandé d’utiliser une [Configuration de mise à l’échelle automatique](manage-cluster-horizontal-scaling.md#optimized-autoscale). Si vous utilisez la mise à l’échelle automatique optimisée et que vous voyez la recommandation de taille sur votre cluster, c’est que votre référence SKU de machine virtuelle actuelle ou les limites du nombre minimal et maximal d’instances de la mise à l’échelle automatique optimisée ne sont pas optimales. Le nombre d’instances recommandé doit être inclus dans les limites que vous avez définies. Pour plus d’informations, consultez les [références (SKU) des machines virtuelles](manage-cluster-choose-sku.md).

> [!TIP]
> La configuration de la mise à l’échelle automatique optimisée ne modifie pas immédiatement le nombre d’instances. Pour des modifications instantanées, utilisez la [mise à l’échelle manuelle](manage-cluster-horizontal-scaling.md#manual-scale) pour réinitialiser le nombre d’instances recommandé, puis activez la mise à l’échelle automatique optimisée pour permettre les optimisations futures.

#### <a name="update-cache-policy-for-azure-data-explorer-tables"></a>Mettre à jour la stratégie de cache pour les tables Azure Data Explorer

La recommandation **Examiner la période de mise en cache des tables Azure Data Explorer (stratégie) pour de meilleures performances** est donnée pour un cluster qui nécessite une période rétrospective (filtre de temps) différente ou une [stratégie de cache](kusto/management/cachepolicy.md) plus étendue. Cette recommandation est basée sur la période de recherche dans l’antériorité des requêtes au cours des 30 derniers jours. La plupart des requêtes exécutées au cours des 30 derniers jours ont accédé à des données qui n’étaient pas dans le cache, ce qui peut augmenter le temps d’exécution de vos requêtes.  Vous voyez les 10 premières tables qui ont accédé à des données hors cache, classées selon le pourcentage des requêtes.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer la mise à l'échelle horizontale d'un cluster (scale-out) dans Azure Data Explorer pour prendre en compte les fluctuations de la demande](manage-cluster-horizontal-scaling.md)
* [Gérer la mise à l'échelle verticale d'un cluster (montée en puissance) dans Azure Data Explorer pour prendre en compte les fluctuations de la demande](manage-cluster-vertical-scaling.md)
