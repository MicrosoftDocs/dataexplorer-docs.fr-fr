---
title: Surveiller Azure Data Explorer à l'aide de Resource Health
description: Utilisez Azure Resource Health pour surveiller les ressources Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 9b8b1b40e972d7719ef2cf495ed12cb4ed3478fd
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492413"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Surveiller Azure Data Explorer à l'aide de Resource Health (préversion)

[Resource Health](/azure/service-health/resource-health-overview) pour Azure Data Explorer vous informe sur l'état d'intégrité de vos ressources Azure Data Explorer et vous fournit des recommandations pratiques pour résoudre les problèmes. Resource Health est mis à jour toutes les 1 à 2 minutes et rend compte de l'intégrité actuelle et passée de vos ressources. 

Resource Health détermine l'état d'intégrité de vos ressources Azure Data Explorer en examinant différentes données, telles que :
* Disponibilité des ressources
* Échecs des requêtes

## <a name="access-resource-health-reporting"></a>Accès aux rapports Resource Health

1. Sur le portail [Azure](https://portal.azure.com/), sélectionnez **Surveiller** dans la liste des services.
1. Sélectionnez **Service Health** > **Resource Health**.
1. Dans la liste déroulante **Abonnement**, sélectionnez votre abonnement. Dans la liste déroulante **Type de ressource**, sélectionnez **Azure Data Explorer**.
1. Le tableau qui s'affiche répertorie toutes les ressources de l'abonnement choisi. Chaque ressource est accompagnée d'une icône d'état d'intégrité.
1. Sélectionnez votre ressource pour afficher son [état d'intégrité](#resource-health-status) et les recommandations correspondantes.

    ![Vue d’ensemble](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>État d’intégrité des ressources

L'intégrité d'une ressource est déterminée par les états suivants : disponible, non disponible et inconnu.

### <a name="available"></a>Disponible

L'état d'intégrité **Disponible** indique que votre ressource Azure Data Explorer est saine et ne présente aucun problème.

![Disponible](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Non disponible

L'état d'intégrité **Non disponible** indique que votre ressource Azure Data Explorer a rencontré un problème qui la rend indisponible pour les requêtes et l'ingestion. Par exemple, les nœuds de votre ressource Azure Data Explorer peuvent avoir redémarré de manière inattendue. Si votre ressource Azure Data Explorer reste dans cet état pendant une période prolongée, contactez le [support]().

![Non disponible](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Unknown

L'état d'intégrité **Inconnu** indique que **Resource Health** n'a reçu aucune information sur cette ressource Azure Data Explorer depuis plus de 10 minutes. Cet état ne constitue pas une indication définitive sur l'intégrité de la ressource Azure Data Explorer mais il s'agit d'un point de données important dans le processus de résolution du problème. Si votre cluster Azure Data Explorer fonctionne comme prévu, l'état passera à **Disponible** en quelques minutes. L'état d'intégrité **Inconnu** peut suggérer qu'un événement de la plateforme influe sur la ressource. 

> [!TIP]
> L'intégrité des ressources du cluster Azure Data Explorer sera **Inconnue** si l'état est « Arrêté ».

![Unknown](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Informations d’historique

Dans le volet **Resource Health** > **Historique d'intégrité**, accédez à un maximum de quatre semaines d'informations sur l'état d'intégrité de la ressource. Cliquez sur la flèche pour obtenir des informations supplémentaires sur les problèmes liés aux événements d'intégrité signalés dans ce volet. 

![Historique](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>Étapes suivantes

* [Configurer des alertes Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Tutoriel : Ingérer et interroger des données de supervision dans Azure Data Explorer](ingest-data-no-code.md)
* [Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques](using-metrics.md)