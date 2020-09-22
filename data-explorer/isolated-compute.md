---
title: Activer le calcul isolé sur votre cluster Azure Data Explorer
description: Dans cet article, vous allez découvrir comment activer le calcul isolé sur votre cluster Azure Data Explorer en sélectionnant la référence SKU appropriée.
author: orspod
ms.author: orspodek
ms.reviewer: dagrawal
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/16/2020
ms.custom: references_regions
ms.openlocfilehash: 8139701037a8d4d25490a355cc822051851c85cd
ms.sourcegitcommit: 313a91d2a34383b5a6e39add6c8b7fabb4f8d39a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90682287"
---
# <a name="enable-isolated-compute-on-your-azure-data-explorer-cluster"></a>Activer le calcul isolé sur votre cluster Azure Data Explorer

Les machines virtuelles de calcul isolé permettent aux clients d’exécuter leur charge de travail dans un environnement isolé du matériel dédié à un client unique. Les clusters déployés avec des machines virtuelles de calcul isolé sont mieux adaptés aux charges de travail qui nécessitent un niveau élevé d’isolation pour les exigences de conformité et de réglementation. Les références SKU de calcul offrent une isolation pour sécuriser les données sans sacrifier la flexibilité de la configuration. Pour plus d’informations, consultez [Isolation de calcul](/azure/security/fundamentals/isolation-choices#compute-isolation) et [Conseils Azure relatifs au calcul isolé](/azure/azure-government/azure-secure-isolation-guidance#compute-isolation). 

Azure Data Explorer prend en charge le calcul isolé avec la référence SKU **Standard_E64i_v3**. Cette référence SKU peut subir un scale-up ou un scale-down automatique afin de répondre aux besoins de votre application ou de votre entreprise. La prise en charge du calcul isolé est disponible dans les régions suivantes :
* USA Ouest 2
* USA Est 
* USA Centre Sud

Les machines virtuelles de calcul isolé, bien que coûteuses, constituent la référence SKU idéale pour l’exécution de charges de travail nécessitant une isolation au niveau de l’instance du serveur. Pour plus d’informations sur les références SKU prises en charge pour Azure Data Explorer, consultez [Sélectionner la bonne référence SKU de machine virtuelle pour votre cluster Azure Data Explorer](manage-cluster-choose-sku.md).

> [!NOTE]
> [Azure Dedicated Host](https://azure.microsoft.com/services/virtual-machines/dedicated-host/) n’est pas pris en charge actuellement par Azure Data Explorer. 

## <a name="enable-isolated-compute-on-azure-data-explorer-cluster"></a>Activer le calcul isolé sur un cluster Azure Data Explorer 

Pour activer le calcul isolé dans Azure Data Explorer, appliquez l’une des procédures suivantes :
* [Créer un cluster avec une référence SKU de calcul isolé](#create-a-cluster-with-isolated-compute-sku)
* [Sélectionner la référence SKU de calcul isolé sur un cluster existant](#select-the-isolated-compute-sku-on-an-existing-cluster)

## <a name="create-a-cluster-with-isolated-compute-sku"></a>Créer un cluster avec une référence SKU de calcul isolé

1. Suivez les instructions pour [créer une base de données et un cluster Azure Data Explorer dans le portail Azure](create-cluster-database-portal.md).
1. Dans [Créer un cluster](create-cluster-database-portal.md#create-a-cluster) sous l’onglet **Informations de base**, sélectionnez **Standard_E64i_v3** dans la liste déroulante **Spécifications de calcul**.

## <a name="select-the-isolated-compute-sku-on-an-existing-cluster"></a>Sélectionner la référence SKU de calcul isolé sur un cluster existant

1. Sur l’écran **Vue d’ensemble** de votre cluster Azure Data Explorer, sélectionnez **Mettre à l’échelle vers le haut**.
1. Dans la zone de recherche, recherchez *Standard_E64i_v3* et cliquez sur le nom de la référence SKU ou sélectionnez la référence **Standard_E64i_v3** dans la liste.
1. Sélectionnez **Appliquer** pour mettre à jour votre référence SKU. 

:::image type="content" source="media/isolated-compute/select-isolated-compute-sku.png" alt-text="Sélectionner la référence SKU de calcul isolé":::

> [!TIP]
> Le processus de scale-up peut prendre quelques minutes.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer la mise à l'échelle verticale d'un cluster (montée en puissance) dans Azure Data Explorer pour prendre en compte les fluctuations de la demande](manage-cluster-vertical-scaling.md)
* [Sélectionner la bonne référence SKU de machine virtuelle pour votre cluster Azure Data Explorer](manage-cluster-choose-sku.md)
