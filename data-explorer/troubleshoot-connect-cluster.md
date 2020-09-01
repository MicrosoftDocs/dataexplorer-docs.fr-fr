---
title: Résoudre les échecs de connexion de cluster Azure Data Explorer
description: Cet article décrit la procédure de résolution des problèmes pour la connexion à un cluster dans Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 406e41728b34d0b0de48d71b1d7c2cf7ce0098bc
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874101"
---
# <a name="troubleshoot-failure-to-connect-to-a-cluster-in-azure-data-explorer"></a>Résolution des problèmes : Échec de connexion à un cluster dans l’Explorateur de données Azure

Si vous n’êtes pas en mesure de vous connecter à un cluster dans l’Explorateur de données Azure, effectuez les étapes suivantes.

1. Vérifiez que la chaîne de connexion est correcte. Elle doit se présenter sous la forme `https://<ClusterName>.<Region>.kusto.windows.net`, comme dans l’exemple suivant : `https://docscluster.westus.kusto.windows.net`.

1. Veillez à disposer des autorisations appropriées. Dans le cas contraire, vous obtiendrez la réponse *non autorisé*.

    Pour plus d’informations sur les autorisations, consultez [Gérer des autorisations de base de données](manage-database-permissions.md). Si nécessaire, demandez à l’administrateur de votre cluster de vous ajouter au rôle approprié.

1. Vérifiez que le cluster n’a pas été supprimé : passez en revue le journal d’activité dans votre abonnement.

1. Consultez le [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/). Recherchez l’état de l’Explorateur de données Azure dans la région où vous tentez de vous connecter à un cluster.

    Si l’état n’est pas **correct** (coche verte), essayez de vous connecter au cluster une fois que l’état s’est amélioré.

1. Si vous avez toujours besoin d’aide pour résoudre votre problème, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).