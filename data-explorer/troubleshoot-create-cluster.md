---
title: Résoudre les problèmes d’échec de création d’un cluster Azure Data Explorer
description: Cet article décrit la procédure de résolution des problèmes pour la création d’un cluster dans l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 1bfec09d8ab953508ce89365f8431e7211dfd9cb
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874917"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Résoudre les problèmes : Échec de création du cluster Azure Data Explorer

Dans le cas peu probable où la création de cluster échouerait dans l’Explorateur de données Azure, effectuez les étapes suivantes.

1. Veillez à disposer des autorisations appropriées. Pour créer un cluster, vous devez être un membre du rôle *Collaborateur* ou *Propriétaire* pour l’abonnement Azure. Si nécessaire, demandez à l’administrateur de votre abonnement de vous ajouter au rôle approprié.

1. Vérifiez l’absence d’erreurs de validation liées au nom de cluster que vous avez entré sous **Créer un cluster** dans le portail Azure.

1. Consultez le [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/). Recherchez l’état de l’Explorateur de données Azure dans la région où vous tentez de créer le cluster.

    Si l’état n’est pas **correct** (coche verte), essayez de créer le cluster une fois que l’état s’est amélioré.

1. Si vous avez toujours besoin d’aide pour résoudre votre problème, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
