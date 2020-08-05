---
title: Sécuriser votre cluster en utilisant le chiffrement de disque dans Azure Data Explorer – Portail Azure
description: Cet article explique comment sécuriser votre cluster en utilisant le chiffrement de disque dans Azure Data Explorer au sein du portail Azure.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/02/2020
ms.openlocfilehash: 4f0ed42727759dae54f75fcb33ba1f024c218a4a
ms.sourcegitcommit: d9fbcd6c9787f90de62e8e832c92d43b8090cbfc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87515899"
---
# <a name="secure-your-cluster-using-disk-encryption-in-azure-data-explorer---azure-portal"></a>Sécuriser votre cluster en utilisant le chiffrement de disque dans Azure Data Explorer – Portail Azure

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) vous aide à protéger et à préserver vos données de façon à répondre aux engagements de votre entreprise en matière de sécurité et de conformité. Il fournit un chiffrement de volume pour le système d’exploitation et les disques de données de vos machines virtuelles de cluster. Il est également intégré à [Azure Key Vault](/azure/key-vault/), ce qui vous permet de contrôler et de gérer les secrets et les clés de chiffrement de disque et de garantir que toutes les données figurant sur les disques des machines virtuelles sont chiffrées. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Activez le chiffrement au repos dans le portail Azure
  
Les paramètres de sécurité de votre cluster vous permettent d’activer le chiffrement de disque sur votre cluster. Activer le [chiffrement des données au repos](/azure/security/fundamentals/encryption-atrest) sur votre cluster offre une protection des données pour les données stockées (au repos). 

1. Dans le portail Azure, accédez à votre ressource de cluster Azure Data Explorer. Sous l’en-tête **Paramètres**, sélectionnez **Sécurité**. 

    ![Activer le chiffrement au repos](media/manage-cluster-security/security-encryption-at-rest.png)

1. Dans la fenêtre **Sécurité**, sélectionnez **Activé** pour le paramètre sécurité du **Chiffrement de disque**. 

1. Sélectionnez **Enregistrer**.
 
> [!NOTE]
> Sélectionnez **Désactivé** pour désactiver le chiffrement une fois qu’il a été activé.

## <a name="azure-data-explorer-stores-data-within-a-region"></a>Azure Data Explorer stocke les données dans une région

Chaque cluster Azure Data Explorer s’exécute sur des ressources dédiées dans une seule région. Toutes les données sont stockées dans la région. 

## <a name="next-steps"></a>Étapes suivantes

[Vérifier l’intégrité des clusters](check-cluster-health.md)
