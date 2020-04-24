---
title: Sélectionner la bonne référence SKU de machine virtuelle pour votre cluster Azure Data Explorer
description: Cet article explique comment sélectionner la taille optimale de référence SKU pour le cluster Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494485"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Sélectionner la bonne référence SKU de machine virtuelle pour votre cluster Azure Data Explorer 

Quand vous créez un cluster ou que vous optimisez un cluster pour une charge de travail changeante, Azure Data Explorer met à votre disposition plusieurs références SKU de machine virtuelle. Les machines virtuelles ont été soigneusement choisies afin que vous bénéficiiez du coût le plus optimal pour n’importe quelle charge de travail. 

La taille et la référence SKU d’une machine virtuelle du cluster de gestion des données sont entièrement gérées par le service Azure Data Explorer. Elles sont déterminées par des facteurs tels que la taille de la machine virtuelle du moteur et la charge de travail d’ingestion. 

Vous pouvez changer la référence SKU de machine virtuelle du cluster moteur à tout moment en [mettant à l’échelle le cluster](manage-cluster-vertical-scaling.md). Il est préférable de commencer par la plus petite taille de référence SKU correspondant au scénario initial. Gardez à l’esprit que la mise à l’échelle du cluster entraîne un temps d’arrêt pouvant atteindre 30 minutes quand le cluster est recréé avec la nouvelle référence SKU de machine virtuelle.

> [!TIP]
> Le calcul des [instances réservées (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) est applicable au cluster Azure Data Explorer.  

Cet article décrit différentes options de référence SKU de machine virtuelle et fournit des détails techniques qui peuvent vous aider à faire le meilleur choix.

## <a name="select-a-cluster-type"></a>Sélectionner un type de cluster

Azure Data Explorer offre deux types de clusters :

* **Production** : Les clusters de production contiennent deux nœuds pour les clusters de moteur et de gestion de données et sont utilisés conformément au [contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/) Azure Data Explorer.

* **Développement/test (aucun SLA)**  : Les clusters de développement/test ne comportent qu’un seul nœud D11 v2 pour le cluster moteur et un seul nœud D1 pour le cluster de gestion des données. Ce type de cluster représente la configuration la moins chère en raison de son faible nombre d’instances et de l’absence de frais de balisage moteur. Il n’existe pas de SLA pour cette configuration de cluster, car elle manque de redondance.

## <a name="sku-types"></a>Types de référence SKU

Quand vous créez un cluster Azure Data Explorer, sélectionnez la référence SKU de machine virtuelle *optimale* pour la charge de travail planifiée. Vous pouvez choisir parmi les deux familles de références SKU Azure Data Explorer suivantes :

* **D v2** : La référence SKU D est optimisée pour le calcul et est disponible en deux versions :
    * La machine virtuelle elle-même
    * La machine virtuelle regroupée avec des disques de stockage Premium

* **LS** : La référence SKU L est optimisée pour le stockage. Pour le même prix, elle propose un disque SSD beaucoup plus grand que la référence SKU D.

Les principales différences entre les types de référence SKU disponibles sont décrites dans le tableau suivant :
 
| Attribut | RÉFÉRENCE SKU D | RÉFÉRENCE SKU L |
|---|---|---
|**Petites références SKU**|La taille minimale est D11 avec deux cœurs|La taille minimale est L4 avec quatre cœurs |
|**Disponibilité**|Disponible dans toutes les régions (la version DS+PS offre une disponibilité plus limitée)|Disponible dans quelques régions |
|**Coût par&nbsp;Go de cache par cœur**|Elevé avec la référence SKU D, faible avec la version DS+PS|Le moins cher avec l’option Paiement à l’utilisation |
|**Prix des instances réservées (RI)**|Remise élevée (plus de 55&nbsp;% pour un engagement sur trois ans)|Remise plus petite (20&nbsp;% pour un engagement sur trois ans) |  

## <a name="select-your-cluster-vm"></a>Sélectionner votre machine virtuelle de cluster 

Pour sélectionner votre machine virtuelle de cluster, [configurez une mise à l'échelle verticale](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

Avec différentes options de référence SKU de machine virtuelle disponibles, vous pouvez optimiser les coûts en fonctions des exigences de performances et de cache à chaud de votre scénario. 
* Si vous avez besoin de performances optimales pour un volume de requêtes élevé, la référence SKU idéale doit être optimisée pour le calcul. 
* Si vous devez interroger de grands volumes de données avec une charge de requête relativement inférieure, la référence SKU optimisée pour le stockage peut contribuer à réduire les coûts tout en offrant d’excellentes performances.

Le nombre d’instances par cluster pour les petites références SKU étant limité, il est préférable d’utiliser des machines virtuelles de plus grande taille avec une plus grande RAM. Davantage de RAM est nécessaire pour certains types de requêtes qui exigent plus de ressources RAM, notamment celles qui utilisent `joins`. Ainsi, quand vous étudiez les options de mise à l’échelle, nous vous recommandons d’effectuer un scale-up au profit d’une référence SKU plus grande plutôt qu’un scale-out en ajoutant des instances.

## <a name="vm-options"></a>Options de la machine virtuelle

Les spécifications techniques des machines virtuelles du cluster Azure Data Explorer sont décrites dans le tableau suivant :

|**Nom**| **Catégorie** | **Taille du disque SSD** | **Cœurs** | **RAM** | **Disques de stockage Premium (1&nbsp;To)**| **Nombre minimum d'instances par cluster** | **Nombre maximum d'instances par cluster**
|---|---|---|---|---|---|---|---
|D11 v2| Optimisé pour le calcul | 75&nbsp;Go    | 2 | 14&nbsp;Go | 0 | 1 | 8 (sauf pour les références SKU de développement/test où il est de 1)
|D12 v2| Optimisé pour le calcul | 150&nbsp;Go   | 4 | 28&nbsp;Go | 0 | 2 | 16
|D13 v2| Optimisé pour le calcul | 307&nbsp;Go   | 8 | 56&nbsp;Go | 0 | 2 | 1 000
|D14 v2| Optimisé pour le calcul | 614&nbsp;Go   | 16| 112&nbsp;Go | 0 | 2 | 1 000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| Optimisé pour le stockage | 1&nbsp;To | 8 | 56&nbsp;Go | 1 | 2 | 1 000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| Optimisé pour le stockage | 2&nbsp;To | 8 | 56&nbsp;Go | 2 | 2 | 1 000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| Optimisé pour le stockage | 3&nbsp;To | 16 | 112&nbsp;Go | 2 | 2 | 1 000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| Optimisé pour le stockage | 4&nbsp;To | 16 | 112&nbsp;Go | 4 | 2 | 1 000
|L4s v1| Optimisé pour le stockage | 650&nbsp;Go | 4 | 32&nbsp;Go | 0 | 2 | 16
|L8s v1| Optimisé pour le stockage | 1,3&nbsp;To | 8 | 64&nbsp;Go | 0 | 2 | 1 000
|L16s_1| Optimisé pour le stockage | 2,6&nbsp;To | 16| 128&nbsp;Go | 0 | 2 | 1 000

* Vous pouvez visualiser la liste mise à jour des références SKU de machine virtuelle par région à l’aide de l’[API ListSkus](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet) Azure Data Explorer. 
* Découvrez-en plus sur les [différentes références SKU](/azure/virtual-machines/windows/sizes). 

## <a name="next-steps"></a>Étapes suivantes

* Vous pouvez [appliquer un scale-up ou un scale-down](manage-cluster-vertical-scaling.md) au cluster moteur à tout moment en changeant la référence SKU de machine virtuelle, en fonction des besoins. 

* Vous pouvez [effectuer un scale-in ou un scale-out](manage-cluster-horizontal-scaling.md) de la taille du cluster moteur pour modifier la capacité, en fonction des demandes.

