---
title: Sélectionner la bonne référence SKU de calcul pour votre cluster Azure Data Explorer
description: Cet article explique comment sélectionner la taille optimale de référence SKU de calcul pour un cluster Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/13/2020
ms.openlocfilehash: 73ba99c71139a38b32b2242d33e9901f3f79df82
ms.sourcegitcommit: ee49cd8186d4aecd5de1ed6d24db6c7b7a079ac4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/16/2021
ms.locfileid: "100548950"
---
# <a name="select-the-correct-compute-sku-for-your-azure-data-explorer-cluster"></a>Sélectionner la bonne référence SKU de calcul pour votre cluster Azure Data Explorer 

Quand vous créez un cluster ou que vous optimisez un cluster pour une charge de travail changeante, Azure Data Explorer met à votre disposition plusieurs références SKU de machine virtuelle. Les références SKU de calcul ont été soigneusement choisies pour vous permettre de bénéficier du coût optimal pour n’importe quelle charge de travail. 

La taille et la référence SKU d’une machine virtuelle du cluster de gestion des données sont entièrement gérées par le service Azure Data Explorer. Elles sont déterminées par des facteurs tels que la taille de la machine virtuelle du moteur et la charge de travail d’ingestion. 

Vous pouvez changer la référence SKU de calcul du cluster du moteur à tout moment en [effectuant un scale-up du cluster](manage-cluster-vertical-scaling.md). Il est préférable de commencer par la plus petite taille de référence SKU correspondant au scénario initial. Gardez à l’esprit que le scale-up du cluster entraîne un temps d’arrêt pouvant atteindre 30 minutes pendant que le cluster est recréé avec la nouvelle référence SKU. Vous pouvez aussi utiliser les [recommandations d’Azure Advisor](azure-advisor.md) pour optimiser votre référence SKU de calcul.

> [!TIP]
> Les [instances réservées de calcul](/azure/virtual-machines/windows/prepay-reserved-vm-instances) sont applicables au cluster Azure Data Explorer.  

Cet article décrit différentes options de référence SKU de calcul et fournit les détails techniques qui peuvent vous aider à faire le meilleur choix.

## <a name="select-a-cluster-type"></a>Sélectionner un type de cluster

Azure Data Explorer offre deux types de clusters :

* **Production** : Les clusters de production contiennent deux nœuds pour les clusters de moteur et de gestion de données et sont utilisés conformément au [contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/) Azure Data Explorer.

* **Développement/test (aucun SLA)**  : Les clusters de développement/test ont un seul nœud pour le moteur et le cluster de gestion des données. Ce type de cluster représente la configuration la moins chère en raison de son faible nombre d’instances et de l’absence de frais de balisage moteur. Il n’existe pas de SLA pour cette configuration de cluster, car elle manque de redondance.

## <a name="compute-sku-types"></a>Types de références SKU de calcul

Le cluster Azure Data Explorer prend en charge une variété de références SKU pour les différents types de charges de travail. Chaque référence SKU offre un ratio de SSD et de processeur distinct pour aider les clients à dimensionner correctement leur déploiement et à créer des solutions optimales en termes de coûts pour leur charge de travail analytique d’entreprise.

### <a name="compute-optimized"></a>Optimisé pour le calcul

* Fournit un ratio cœur/cache élevé.
* Adapté à un débit élevé de requêtes sur des tailles de données petites à moyennes.
* SSD local pour des E/S à faible latence.

### <a name="heavy-compute"></a>Calcul intensif

* Références SKU AMD qui offrent un ratio cœur/cache plus élevé.
* SSD local pour des E/S à faible latence.

### <a name="storage-optimized"></a>Optimisé pour le stockage

* Option pour un stockage plus grand, allant de 1 à 4 To par nœud de moteur.
* Adapté aux charges de travail qui nécessitent le stockage d’un volume important de données avec des exigences de requête de calcul moins intensives.
* Certaines références SKU utilisent un stockage Premium (disque managé) attaché au nœud de moteur au lieu d’un SSD local pour le stockage de données chaudes.

### <a name="isolated-compute"></a>Calcul isolé

Référence SKU idéale pour l’exécution de charges de travail nécessitant une isolation au niveau de l’instance du serveur.

## <a name="select-and-optimize-your-compute-sku"></a>Sélectionner et optimiser votre référence SKU de calcul 

### <a name="select-your-compute-sku-during-cluster-creation"></a>Sélectionner votre référence SKU de calcul lors de la création du cluster

Quand vous créez un cluster Azure Data Explorer, sélectionnez la référence SKU de machine virtuelle *optimale* pour la charge de travail planifiée.

Les attributs suivants peuvent également vous aider à choisir la référence SKU :
 
| Attribut | Détails |
|---|---
|**Disponibilité**| Certaines références SKU ne sont pas disponibles dans toutes les régions. |
|**Coût par Go de cache par cœur**| Coût élevé avec calcul et calcul intensif optimisés. Faible coût avec les références SKU optimisées pour le stockage |
|**Prix des instances réservées (RI)**| La remise pour les instances réservées varie selon la région et la référence SKU |  

> [!NOTE]
> Pour le cluster Azure Data Explorer, le coût de calcul est la partie la plus importante du coût du cluster par rapport au stockage et au réseau.

### <a name="optimize-your-cluster-compute-sku"></a>Optimiser la référence SKU de calcul de votre cluster

Pour optimiser la référence SKU de calcul de votre cluster, [configurez la mise à l’échelle verticale](manage-cluster-vertical-scaling.md#configure-vertical-scaling) et vérifiez les [recommandations d’Azure Advisor](azure-advisor.md). 

Avec les différentes options de référence SKU de calcul disponibles, vous pouvez optimiser les coûts en fonctions des exigences de performances et de cache de données chaudes de votre scénario. 
* Si vous avez besoin de performances optimales pour un volume de requêtes élevé, la référence SKU idéale doit être optimisée pour le calcul. 
* Si vous devez interroger de grands volumes de données avec une charge de requête relativement inférieure, la référence SKU optimisée pour le stockage peut contribuer à réduire les coûts tout en offrant d’excellentes performances.

Le nombre d’instances par cluster pour les petites références SKU étant limité, il est préférable d’utiliser des machines virtuelles de plus grande taille avec une plus grande RAM. Davantage de RAM est nécessaire pour certains types de requêtes qui exigent plus de ressources RAM, notamment celles qui utilisent `joins`. C’est pourquoi, quand vous examinez les options de mise à l’échelle, nous vous recommandons d’effectuer un scale-up vers une référence SKU plus grande au lieu d’un scale-out en ajoutant des instances.

## <a name="compute-sku-options"></a>Options de référence SKU de calcul

Les spécifications techniques des machines virtuelles du cluster Azure Data Explorer sont décrites dans le tableau suivant :

|**Nom**| **Catégorie** | **Taille du disque SSD** | **Cœurs** | **RAM** | **Disques de stockage Premium (1&nbsp;To)**| **Nombre minimum d'instances par cluster** | **Nombre maximum d'instances par cluster**
|---|---|---|---|---|---|---|---
|Dev(No SLA) Standard_D11_v2| Optimisé pour le calcul | 80&nbsp;Go    | 1 | 14&nbsp;Go | 0 | 1 | 1
|Dev(No SLA) Standard_E2a_v4| Optimisé pour le calcul | 24&nbsp;Go    | 1 | 16&nbsp;Go | 0 | 1 | 1
|Standard_D11_v2| Optimisé pour le calcul | 80&nbsp;Go    | 2 | 14&nbsp;Go | 0 | 2 | 8 
|Standard_D12_v2| Optimisé pour le calcul | 160&nbsp;Go   | 4 | 28&nbsp;Go | 0 | 2 | 16
|Standard_D13_v2| Optimisé pour le calcul | 317&nbsp;Go   | 8 | 56&nbsp;Go | 0 | 2 | 1 000
|Standard_D14_v2| Optimisé pour le calcul | 628&nbsp;Go   | 16| 112&nbsp;Go | 0 | 2 | 1 000
|Standard_E2a_v4| calcul intensif | 24&nbsp;Go    | 2 | 16&nbsp;Go | 0 | 2 | 8 
|Standard_E4a_v4| calcul intensif | 60&nbsp;Go   | 4 | 32&nbsp;Go | 0 | 2 | 16
|Standard_E8a_v4| calcul intensif | 137&nbsp;Go   | 8 | 64&nbsp;Go | 0 | 2 | 1 000
|Standard_E16a_v4| calcul intensif | 273&nbsp;Go   | 16| 128&nbsp;Go | 0 | 2 | 1 000
|Standard_DS13_v2 + 1&nbsp;To&nbsp;PS| Optimisé pour le stockage | 1&nbsp;To | 8 | 56&nbsp;Go | 1 | 2 | 1 000
|Standard_DS13_v2 + 2&nbsp;To&nbsp;PS| Optimisé pour le stockage | 2&nbsp;To | 8 | 56&nbsp;Go | 2 | 2 | 1 000
|Standard_DS14_v2 + 3&nbsp;To&nbsp;PS| Optimisé pour le stockage | 3&nbsp;To | 16 | 112&nbsp;Go | 2 | 2 | 1 000
|Standard_DS14_v2 + 4&nbsp;To&nbsp;PS| Optimisé pour le stockage | 4&nbsp;To | 16 | 112&nbsp;Go | 4 | 2 | 1 000
|Standard_E8as_v4 + 1&nbsp;To&nbsp;PS| Optimisé pour le stockage | 1&nbsp;To | 8 | 64&nbsp;Go | 1 | 2 | 1 000
|Standard_E8as_v4 + 2&nbsp;To&nbsp;PS| Optimisé pour le stockage | 2&nbsp;To | 8 | 64&nbsp;Go | 2 | 2 | 1 000
|Standard_E16as_v4 + 3&nbsp;To&nbsp;PS| Optimisé pour le stockage | 3&nbsp;To | 16 | 128&nbsp;Go | 3 | 2 | 1 000
|Standard_E16as_v4 + 4&nbsp;To&nbsp;PS| Optimisé pour le stockage | 4&nbsp;To | 16 | 128&nbsp;Go | 4 | 2 | 1 000
|Standard_L4s| Optimisé pour le stockage | 650&nbsp;Go | 4 | 32&nbsp;Go | 0 | 2 | 16
|Standard_L8s| Optimisé pour le stockage | 1,3&nbsp;To | 8 | 64&nbsp;Go | 0 | 2 | 1 000
|Standard_L16s| Optimisé pour le stockage | 2,6&nbsp;To | 16| 128&nbsp;Go | 0 | 2 | 1 000
|Standard_L8s_v2| Optimisé pour le stockage | 1,7&nbsp;To | 8 | 64&nbsp;Go | 0 | 2 | 1 000
|Standard_L16s_v2| Optimisé pour le stockage | 3,5&nbsp;To | 16| 128&nbsp;Go | 0 | 2 | 1 000
|Standard_E64i_v3| calcul isolé | 1,1&nbsp;To | 64 | 432&nbsp;Go | 0 | 2 | 1 000
|Standard_E80ids_v4| calcul isolé | 1,8&nbsp;To | 80 | 504&nbsp;Go | 0 | 2 | 1 000

* Vous pouvez visualiser la liste mise à jour des références SKU de calcul par région en utilisant l’[API ListSkus](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus) d’Azure Data Explorer. 
* Découvrez-en plus sur les [différentes références SKU](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez [appliquer un scale-up ou un scale-down](manage-cluster-vertical-scaling.md) au cluster moteur à tout moment en changeant la référence SKU de machine virtuelle, en fonction des besoins. 

* Vous pouvez [effectuer un scale-in ou un scale-out](manage-cluster-horizontal-scaling.md) de la taille du cluster moteur pour modifier la capacité, en fonction des demandes.

* Utilisez les [recommandations d’Azure Advisor](azure-advisor.md) pour optimiser votre référence SKU de calcul.
