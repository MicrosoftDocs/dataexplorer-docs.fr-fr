---
title: Utiliser Azure Data Share pour partager des données avec Azure Data Explorer
description: Découvrez comment partager vos données avec Azure Data Explorer et Azure Data Share.
author: orspod
ms.author: orspodek
ms.reviewer: maraheja
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/14/2020
ms.openlocfilehash: 01c8edee98a8f44ae4ad480cb14a327c4edcee83
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88873183"
---
# <a name="use-azure-data-share-to-share-data-with-azure-data-explorer"></a>Utiliser Azure Data Share pour partager des données avec Azure Data Explorer

Il existe plusieurs façons de partager des données : partages de fichiers, FTP, e-mail, API, etc. Ces méthodes traditionnelles nécessitent que les deux parties créent et gèrent un pipeline de données qui déplace les données entre les équipes et les organisations. Azure Data Explorer vous permet de partager vos données de manière simple et sécurisée avec des personnes de votre entreprise ou des partenaires externes. Le partage se produit en quasi-temps réel, sans nécessiter la création ou la gestion d’un pipeline de données. Toutes les modifications apportées côté fournisseur à la base de données, notamment celles affectant le schéma et les données, sont instantanément disponibles côté client.

[![Vidéo Azure Friday](https://img.youtube.com/vi/Q3MJv90PegE/0.jpg)](https://www.youtube.com/watch?v=Q3MJv90PegE?&autoplay=1)

Azure Data Explorer dissocie le stockage et le calcul, ce qui permet aux clients d’exécuter plusieurs instances de calcul (en lecture seule) sur le même stockage sous-jacent. Vous pouvez attacher une base de données en tant que [base de données abonnée](follower.md), c’est-à-dire une base de données en lecture seule sur un cluster distant.

## <a name="configure-data-sharing"></a>Configurer le partage de données 

Utilisez l’une des méthodes suivantes pour configurer le partage de données :

* Utilisez [Azure Data Share](/azure/data-share/) pour envoyer et gérer des invitations et des partages au sein de l’entreprise ou avec des partenaires et clients externes. Azure Data Share utilise une [base de données abonnée](follower.md) pour créer un lien symbolique entre les clusters Azure Data Explorer du fournisseur et du consommateur. Cette option vous permet de voir et de gérer dans un seul volet tous vos partages de données, qu’ils soient stockés dans des clusters Azure Data Explorer ou dans d’autres services de données. Azure Data Share vous permet également de partager des données entre plusieurs organisations dans différents locataires Azure Active Directory.
* Configurez directement la [base de données abonnée](follower.md). Procédez uniquement de la sorte si vous avez besoin d’une capacité de calcul supplémentaire pour effectuer un scale-out à des fins de rapport et si vous administrez les deux clusters.

> [!Note] 
> Quand la relation de partage est établie, Azure Data Share crée un lien symbolique entre le cluster Azure Data Explorer du fournisseur et celui du consommateur. Si le fournisseur de données révoque l’accès, le lien symbolique est supprimé et la ou les bases de données partagées ne sont plus accessibles au consommateur de données.

:::image type="content" source="media/data-share/adx-datashare-image.png" alt-text="Partage de données dans Azure Data Explorer":::

Le fournisseur de données peut partager les données au niveau de la base de données ou du cluster. Le cluster qui partage la base de données est le cluster leader et celui qui reçoit le partage est le cluster abonné. Un cluster abonné peut suivre une ou plusieurs bases de données d’un cluster leader. Le cluster abonné se synchronise à intervalles réguliers pour vérifier si des modifications ont été apportées. Le décalage entre le leader et l’abonné varie de quelques secondes à quelques minutes en fonction de la taille globale des métadonnées et des données. Les données sont mises en cache sur le cluster consommateur et ne sont disponibles que pour les opérations de lecture ou d’interrogation, à l’exception du remplacement de la stratégie de mise en cache à chaud et des autorisations de base de données. Les requêtes s’exécutant sur le cluster abonné se servent du cache local et n’utilisent pas les ressources du cluster leader.

## <a name="prerequisites"></a>Prérequis

1. Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
1. [Créez un cluster et une base de données dans le fournisseur](create-cluster-database-portal.md) pour le leader et l’abonné.
1. [Ingérez des données](ingest-sample-data.md) dans la base de données de responsable à l’aide de l’une des différentes méthodes présentées dans [Vue d’ensemble de l’ingestion](ingest-data-overview.md).

## <a name="data-provider---share-data"></a>Fournisseur de données : partager des données

Suivez les instructions de la vidéo pour créer un compte de partage de données, ajouter un jeu de données et envoyer une invitation.

[![Fournisseur de données : partager des données](https://img.youtube.com/vi/QmsTnr90_5o/0.jpg)](https://youtu.be/QmsTnr90_5o?&autoplay=1)

## <a name="data-consumer---receive-data"></a>Consommateur de données : recevoir des données

Suivez les instructions de la vidéo pour accepter l’invitation, créer un compte de partage de données et mapper au cluster consommateur.

[![Consommateur de données : recevoir des données](https://img.youtube.com/vi/vBq6iFaCpdA/0.jpg)](https://youtu.be/vBq6iFaCpdA?&autoplay=1)

Le consommateur de données peut maintenant accéder à son cluster Azure Data Explorer pour accorder des autorisations utilisateur aux bases de données partagées et accéder aux données. Les données ingérées en mode batch dans le cluster Azure Data Explorer source apparaissent dans le cluster cible dans un délai de quelques secondes à quelques minutes.

## <a name="limitations"></a>Limites

* [Limitations des bases de données abonnées](follower.md#limitations)

## <a name="next-steps"></a>Étapes suivantes

* [Documentation Azure Data Share](/azure/data-share/)
* Pour plus d’informations sur le cluster abonné, consultez cette [rubrique](follower.md).
