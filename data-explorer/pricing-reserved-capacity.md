---
title: Prépayer la majoration d’Azure Data Explorer pour réaliser des économies
description: Découvrez comment acheter une capacité de réserve Azure Data Explorer pour économiser sur vos coûts Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: avnera
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/03/2019
ms.openlocfilehash: e954b66c59e480d7fc713841e9a029da61dabd8f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350534"
---
# <a name="prepay-for-azure-data-explorer-markup-units-with-azure-data-explorer-reserved-capacity"></a>Prépayer des unités de majoration Azure Data Explorer avec la capacité de réserve Azure Data Explorer

Faites des économies avec Azure Data Explorer en prépayant les unités de majoration au lieu d’appliquer le tarif du paiement à l’utilisation. Grâce à une capacité de réserve Azure Data Explorer, vous vous engagez à utiliser Azure Data Explorer sur une période d’un an ou de trois ans, afin de bénéficier d’une remise importante sur les coûts de majoration d’Azure Data Explorer. Pour acheter une capacité de réserve Azure Data Explorer, il vous suffit de spécifier la durée, et elle s’appliquera à tous les déploiements d’Azure Data Explorer dans toutes les régions.

En achetant une réservation, vous prépayez les coûts majorés pour une période d’un an ou de trois ans. Dès que vous achetez une réservation, les frais de majoration d’Azure Data Explorer qui correspondent aux attributs de la réservation ne sont plus facturés au tarif du paiement à l’utilisation. Les clusters Azure Data Explorer qui sont déjà en cours d’exécution ou récemment déployés bénéficient automatiquement de cet avantage. Cette réservation ne couvre pas les frais de calcul, de stockage ou de mise en réseau associés à l’utilisation des clusters. La capacité de réserve pour ces ressources doit être achetée séparément. À l’issue de la durée de réservation, l’avantage de facturation expire et les unités de majoration Azure Data Explorer sont facturées au tarif du paiement à l’utilisation. Les réservations ne sont pas renouvelées automatiquement. Pour plus d’informations sur la tarification, consultez la [page de tarification d’Azure Data Explorer](https://azure.microsoft.com/pricing/details/data-explorer/).

Vous pouvez acheter une capacité de réserve Azure Data Explorer sur le [Portail Azure](https://portal.azure.com). Pour acheter une capacité de réserve Azure Data Explorer :

* Vous devez être le propriétaire d’au moins un abonnement Entreprise ou un abonnement Paiement à l’utilisation.
* Pour les abonnements Entreprise, **Add Reserved Instances** (Ajouter des instances réservées) doit être activé dans le [portal EA](https://ea.azure.com). Sinon, si ce paramètre est désactivé, vous devez être administrateur EA de l’abonnement.
* Pour le programme Fournisseur de solutions cloud (CSP), seuls des agents d’administration ou de agents commerciaux peuvent acheter une capacité de réserve Azure Data Explorer.

Pour plus d’informations sur la facturation d’achats de réservations aux clients d’Entreprise et aux clients avec Paiement à l’utilisation, consultez :
* [Comprendre l’utilisation d’une réservation Azure pour votre accord de mise en œuvre Entreprise](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) 
* [Comprendre l’utilisation d’une réservation Azure pour votre abonnement avec Paiement à l’utilisation](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)

## <a name="determine-the-right-markup-usage-before-purchase"></a>Déterminer l’utilisation de majoration appropriée avant l’achat

La taille de la réservation doit être basée sur le nombre total d’unités de majoration Azure Data Explorer utilisées par les clusters Azure Data Explorer existants ou bientôt déployés. Le nombre d’unités de majoration est égal au nombre de cœurs de cluster de moteur Azure Data Explorer en production (à l’exclusion de la référence SKU dev/test). 

## <a name="buy-azure-data-explorer-reserved-capacity"></a>Acheter une capacité de réserve Azure Data Explorer

1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Sélectionnez **Tous les services** > **Réservations** > **Acheter**. Sélectionnez **Ajouter**
1. Dans le volet **Sélectionner le type de produit**, sélectionnez **Azure Data Explorer** pour acheter une nouvelle réservation pour les unités de majoration Azure Data Explorer. 
1. Sélectionnez **Acheter**
1. Renseignez les champs obligatoires. 

    ![Page d’achat](media/pricing-reserved-capacity/purchase-page.png)

1. Vérifiez le coût de la réservation de capacité de réserve pour la majoration Azure Data Explorer dans la section **Coûts**.
1. Sélectionnez **Achat**.
1. Sélectionnez **Afficher cette réservation** pour connaître l’état de votre achat.

## <a name="cancellations-and-exchanges"></a>Annulations et échanges

Si vous devez annuler votre réservation de capacité de réserve Azure Data Explorer, des frais de résiliation anticipée de 12 % sont susceptibles d’être appliqués. Les remboursements sont basés sur votre prix d’achat ou le prix actuel de la réservation, le tarif le plus bas étant appliqué. Les remboursements sont limités à 50 000 $ par an. Le remboursement que vous recevez correspond au solde restant au prorata moins les frais de résiliation anticipée de 12 %. Pour demander une annulation, accédez à la réservation dans le Portail Azure et sélectionnez **Remboursement** pour créer une demande de support.

Si vous devez modifier la durée de votre réservation de capacité de réserve Azure Data Explorer, vous pouvez l’échanger contre une autre réservation de valeur égale ou supérieure. La date de début du terme de la nouvelle réservation ne couvre pas la réservation échangée. La période de 1 ou 3 ans commence lorsque vous créez la nouvelle réservation. Pour demander un échange, accédez à la réservation dans le Portail Azure et sélectionnez **Échange** pour créer une demande de support.

Pour plus d’informations sur l’échange ou le remboursement des réservations, voir [Échanges et remboursements de réservations](/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="manage-your-reserved-capacity-reservation"></a>Gérer votre réservation de capacité de réserve

La remise accordée sur la réservation d’unités de majoration Azure Data Explorer est appliquée automatiquement au nombre d’unités de majoration qui correspondent à l’étendue et aux attributs de la réservation de capacité de réserve Azure Data Explorer. 


> [!NOTE]
> * Vous pouvez mettre à jour l’étendue de la réservation de capacité de réserve Azure Data Explorer par le biais du [Portail Azure](https://portal.azure.com), de PowerShell, de CLI ou de l’API.
> * Pour savoir comment gérer la réservation de capacité de réserve Azure Data Explorer, consultez [Gérer la capacité de réserve Azure Data Explorer](/azure/cost-management-billing/reservations/understand-azure-data-explorer-reservation-charges).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les réservations Azure, consultez les articles suivants :

* [Qu’est-ce qu’une réservation Azure ?](/azure/cost-management-billing/reservations/save-compute-costs-reservations)
* [Gérer les réservations Azure](/azure/cost-management-billing/reservations/manage-reserved-vm-instance)
* [Comprendre la remise sur réservation Azure](/azure/cost-management-billing/reservations/understand-reservation-charges)
* [Comprendre l’utilisation d’une réservation pour votre abonnement avec paiement à l’utilisation](/azure/cost-management-billing/reservations/understand-reserved-instance-usage)
* [Comprendre l’utilisation d’une réservation pour votre Accord de Mise en Œuvre Entreprise](/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea)
* [Réservations Azure dans le cadre du programme Fournisseur de solutions Cloud de l’Espace partenaires](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Vous avez besoin d’aide ? Nous contacter

Si vous avez des questions ou besoin d’aide, [créez une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
