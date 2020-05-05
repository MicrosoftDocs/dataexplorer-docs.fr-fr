---
title: Gérer la mise à l’échelle horizontale (scale-out) d’un cluster pour répondre à la demande dans Azure Data Explorer
description: Cet article décrit les étapes à suivre pour effectuer un scale-out et un scale-in dans un cluster Azure Data Explorer en fonction des fluctuations de la demande.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 18bd73797070ba569d1a43a8c66ab92526682f36
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82619157"
---
# <a name="manage-cluster-horizontal-scaling-scale-out-in-azure-data-explorer-to-accommodate-changing-demand"></a>Gérer la mise à l'échelle horizontale d'un cluster (scale-out) dans Azure Data Explorer pour prendre en compte les fluctuations de la demande

Dimensionner un cluster de manière appropriée est essentiel pour les performances de l’Explorateur de données Azure. Une taille de cluster statique peut conduire à une sous-utilisation ou à une surutilisation, ce qui n’est idéal dans aucun des cas. La demande de cluster n'étant pas prévisible de manière précise, la meilleure approche consiste à *mettre à l’échelle* un cluster, en ajoutant et en supprimant de la capacité et des ressources d’UC en fonction des fluctuations de la demande. 

Il existe deux workflows pour la mise à l’échelle d’un cluster Azure Data Explorer : 
* Mise à l’échelle horizontale, également appelée scale-in et scale-out.
* [Mise à l’échelle verticale](manage-cluster-vertical-scaling.md), également appelée scale-up et scale-down.
Cet article explique le workflow de mise à l’échelle horizontale.

## <a name="configure-horizontal-scaling"></a>Configurer la mise à l’échelle horizontale

La mise à l’échelle horizontale vous permet de mettre à l’échelle le nombre d’instances automatiquement en fonction de planifications et de règles prédéfinies. Pour spécifier les paramètres de mise à l’échelle automatique pour votre cluster :

1. Dans le portail Azure, accédez à votre ressource de cluster Azure Data Explorer. Sous **Paramètres**, sélectionnez **Scale-out**. 

2. Dans la fenêtre **Scale-out**, sélectionnez la méthode de mise à l’échelle automatique de votre choix : **Mise à l’échelle manuelle**, **Mise à l’échelle automatique optimisée** ou **Mise à l’échelle personnalisée**.

### <a name="manual-scale"></a>Mise à l’échelle manuelle

La mise à l’échelle manuelle est le paramétrage par défaut lors de la création du cluster. Le cluster a une capacité statique qui ne change pas automatiquement. Vous sélectionnez la capacité statique à l’aide de la barre **Nombre d’instances**. La mise à l’échelle du cluster reste à ce paramètre jusqu’à ce que vous apportiez une autre modification.

   ![Méthode de mise à l’échelle manuelle](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale-preview"></a>Mise à l’échelle automatique (préversion)

La mise à l’échelle automatique optimisée est la méthode de mise à l’échelle automatique recommandée. Cette méthode optimise les performances et les coûts du cluster. Si le cluster approche un état de sous-utilisation, il sera mis à l’échelle en conséquence. Cette action réduit les coûts tout en conservant le niveau de performance. Si le cluster approche un état de surutilisation, il sera mis à l’échelle horizontalement pour assurer des performances optimales. Pour configurer la mise à l’échelle automatique optimisée :

1. Sélectionnez **Mise à l’échelle automatique optimisée**. 

1. Sélectionnez un nombre minimal d’instances et un nombre maximal d’instances. La mise à l’échelle automatique du cluster est comprise entre ces deux nombres, en fonction du chargement.

1. Sélectionnez **Enregistrer**.

   ![Méthode de mise à l’échelle automatique optimisée](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

La mise à l’échelle automatique optimisée commence. Ses actions sont désormais visibles dans le journal d’activité Azure du cluster.

#### <a name="logic-of-optimized-autoscale"></a>Logique de la mise à l’échelle automatique optimisée 

**Scale-out**

Si votre cluster approche un état de surutilisation, effectuez un scale-out pour qu’il conserve des performances optimales. Le scale-out se produit dans les cas suivants :
* Le nombre d’instances de cluster est inférieur au nombre maximal d’instances défini par l’utilisateur.
* L’utilisation du cache est élevée pendant plus d’une heure.
* L'UC reste élevée pendant plus d'une heure.
* L’utilisation de l’ingestion est élevée pendant plus d’une heure.


**Scale-in**

Si votre cluster approche un état de sous-utilisation, effectuez un scale-in pour réduire les coûts tout en maintenant les performances. Plusieurs mesures sont utilisées pour vérifier qu’il est possible d’effectuer un scale-in du cluster en toute sécurité. Les règles suivantes sont évaluées toutes les heures pendant six heures avant la diminution de la taille des instances :
* Le nombre d’instances est supérieur à 2 et excède le nombre minimum d’instances défini.
* Pour garantir qu’il n’y a pas de surcharge des ressources, vérifiez les mesures suivantes avant d’effectuer un scale-in : 
    * L’utilisation du cache n’est pas élevée
    * L’UC est inférieur à la moyenne 
    * L’utilisation de l’ingestion est inférieure à la moyenne 
    * L’utilisation de l’ingestion en continu (si elle est utilisée) n’est pas élevée
    * Les événements Keep Alive se trouvent au-dessus d’un minimum défini, sont traités correctement et à temps.
    * Aucune limitation de requêtes 
    * Le nombre de requêtes ayant échoué est inférieur à la valeur minimale définie.

> [!NOTE]
> La logique de scale-in nécessite actuellement une évaluation de 1 jours avant l’implémentation du scale-in optimisé. Cette évaluation a lieu toutes les 6 heures. Si vous souhaitez effectuer un changement immédiat, utilisez la [mise à l’échelle manuelle](#manual-scale).

### <a name="custom-autoscale"></a>Mise à l’échelle automatique personnalisée

Utiliser la méthode de mise à l’échelle automatique personnalisée vous permet de mettre à l’échelle votre cluster dynamiquement en fonction de métriques que vous spécifiez. Le graphique suivant illustre le flux et les étapes de la configuration d’une mise à l’échelle automatique personnalisée. Voir les détails sous le graphique.

1. Dans la zone **Nom du paramètre de mise à l’échelle automatique**, indiquez un nom, par exemple *Scale-out : utilisation de cache*. 

   ![Règle de mise à l’échelle](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Pour **Mode de mise à l’échelle**, sélectionnez **Mettre à l’échelle selon une mesure**. Ce mode fournit une mise à l’échelle dynamique. Vous pouvez également sélectionner **Mettre à l’échelle à un nombre d’instances spécifique**.

3. Sélectionnez **+ Ajouter une règle**.

4. Dans la section **Règle de mise à l’échelle** à droite, entrez des valeurs pour chaque paramètre.

    **Critères**

    | Paramètre | Description et valeur |
    | --- | --- |
    | **Agrégation du temps** | Sélectionnez un critère d’agrégation, comme **Moyenne**. |
    | **Nom de la métrique** | Sélectionnez la métrique sur laquelle vous voulez baser l’opération de mise à l’échelle, comme **Utilisation du cache**. |
    | **Statistiques de fragment de temps** | Choisissez entre **Moyenne**, **Minimum**, **Maximum** et **Somme**. |
    | **Opérateur** | Choisissez l’option appropriée, par exemple **Supérieur ou égal à**. |
    | **Seuil** | Choisissez une valeur appropriée. Par exemple, pour l’utilisation du cache, 80 pour cent est un bon point de départ. |
    | **Durée (en minutes)** | Choisissez une durée appropriée pendant laquelle le système effectue des recherches lors du calcul de métriques. Commencez avec la valeur par défaut, 10 minutes. |
    |  |  |

    **Action**

    | Paramètre | Description et valeur |
    | --- | --- |
    | **opération** | Choisissez l’option appropriée pour effectuer un scale-in ou un scale-out. |
    | **Nombre d’instances** | Choisissez le nombre de nœuds ou d’instances que vous voulez ajouter ou supprimer quand une condition de métrique est remplie. |
    | **Refroidissement (minutes)** | Choisissez un intervalle de temps d’attente approprié entre les opérations de mise à l’échelle. Commencez avec la valeur par défaut, cinq minutes. |
    |  |  |

5. Sélectionnez **Ajouter**.

6. Dans la section **Limites d’instance** à gauche, entrez des valeurs pour chaque paramètre.

    | Paramètre | Description et valeur |
    | --- | --- |
    | **Minimum** | Nombre d’instances en dessous duquel la mise à l’échelle de votre cluster ne se met pas en œuvre, quelle que soit l’utilisation. |
    | **Maximum** | Nombre d’instances au-dessus duquel la mise à l’échelle de votre cluster ne se met pas en œuvre, quelle que soit l’utilisation. |
    | **Par défaut** | Nombre d’instances par défaut. Ce paramètre est utilisé s’il existe des problèmes de lecture des métriques de ressource. |
    |  |  |

7. Sélectionnez **Enregistrer**.

Vous venez de configurer une mise à l'échelle horizontale pour votre cluster Azure Data Explorer. Ajoutez une autre règle pour une mise à l’échelle verticale. Si vous avez besoin d’aide pour résoudre des problèmes de mise à l’échelle de clusters, [ouvrez une demande de support dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Étapes suivantes

* [Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques](using-metrics.md)
* [Gérer la mise à l’échelle verticale du cluster](manage-cluster-vertical-scaling.md) pour dimensionner ce dernier de manière appropriée.
