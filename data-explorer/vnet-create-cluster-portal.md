---
title: Créer un cluster Azure Data Explorer et une base de données dans votre réseau virtuel
description: Dans cet article, vous allez apprendre à créer un cluster Azure Data Explorer dans votre réseau virtuel.
author: orspod
ms.author: orspodek
ms.reviewer: basaba
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: 99c32122fc48aaea7428fa559d7289713849f34e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494013"
---
# <a name="create-an-azure-data-explorer-cluster-in-your-virtual-network"></a>Créer un cluster Azure Data Explorer dans votre réseau virtuel

Azure Data Explorer prend en charge le déploiement d’un cluster dans un sous-réseau de votre réseau virtuel (VNet). Cette fonctionnalité vous permet d’accéder en privé au cluster depuis votre réseau virtuel Azure ou une ressource d’accès locale, comme Event Hub et le stockage dans votre réseau virtuel, et de restreindre le trafic entrant et sortant.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-network-security-group-nsg"></a>Création d’un groupe de sécurité réseau (NSG)

Les [groupes de sécurité réseau (NSG)](/azure/virtual-network/security-overview) permettent de contrôler l’accès réseau au sein d’un réseau virtuel. Accédez à Azure Data Explorer à l’aide de deux points de terminaison HTTPs (443) et TDS (1433). Les règles NSG suivantes doivent être configurées pour autoriser l’accès à ces points de terminaison pour la gestion, la surveillance et le fonctionnement correct de votre cluster.

Création du groupe de sécurité réseau :

1. Sélectionnez le bouton **+ Créer une ressource** dans le coin supérieur gauche du portail.
1. Recherchez *Groupes de sécurité réseau*.
1. Sous **Groupe de sécurité réseau**, en bas de l’écran, sélectionnez **Créer**.
1. Dans la fenêtre **Créer un groupe de sécurité réseau**, renseignez les informations suivantes.

   ![Formulaire Créer un groupe de sécurité réseau](media/vnet-create-cluster-portal/network-security-group.png)

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre cluster.|
    | Resource group | Votre groupe de ressources | Utilisez un groupe de ressources existant ou créez-en un. |
    | Nom | AzureDataExplorerNsg | Choisissez un nom qui identifie votre groupe de sécurité réseau (NSG) dans le groupe de ressources.  |
    | Région | *USA Ouest* | Sélectionnez la région qui répond le mieux à vos besoins.
    | | | |

1. Sélectionnez **Vérifier + créer** pour passer en revue les détails de votre cluster, puis **Créer** pour le provisionner.

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

    ![Accéder à la ressource](media/vnet-create-cluster-portal/notification-nsg.png)

1. Dans l’onglet **Règles de sécurité de trafic entrant**, sélectionnez **+Ajouter**.
1. Dans la fenêtre **Ajouter une règle de sécurité de trafic entrant**, renseignez les informations suivantes.

    ![Formulaire Créer une règle de trafic entrant NSG](media/vnet-create-cluster-portal/nsg-inbound-rule.png)

    **Paramètre** | **Valeur suggérée** 
    |---|---|
    | Source | ServiceTag
    | Balise du service source | AzureDataExplorerManagement
    | Source port ranges | *
    | Destination | VirtualNetwork
    | Plages de ports de destination | *
    | Protocol | TCP
    | Action | Allow
    | Priority | 100
    | Nom | AllowAzureDataExplorerManagement
    | | |
    
1. Répétez les deux étapes précédentes pour toutes les dépendances entrantes et sortantes en fonction des [dépendances de déploiement du réseau virtuel](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment). Il est également possible de remplacer les règles de trafic sortant par une seule règle pour autoriser *Internet* pour les ports 443 et 80.
    
    Les règles du groupe de sécurité réseau pour les dépendances entrantes et sortantes doivent ressembler à ceci :

    ![règles de groupe de sécurité réseau](media/vnet-create-cluster-portal/nsg-rules.png)

## <a name="create-public-ip-addresses"></a>Créer des adresses IP publiques

Pour créer les adresses IP publiques de requête (moteur) :

1. Sélectionnez le bouton **+ Créer une ressource** dans le coin supérieur gauche du portail.
1. Recherchez *Groupes de sécurité réseau*.
1. Sous **Adresse IP publique**, en bas de l’écran, sélectionnez **Créer**.
1. Dans le volet **Créer une adresse IP publique**, renseignez les informations suivantes.
   
   ![Formulaire Créer une adresse IP publique](media/vnet-create-cluster-portal/public-ip-blade.png)

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Version de l’adresse IP | IPv4 | Sélectionnez la version IP. Nous prenons uniquement en charge l’IPv4.|
    | Sku | standard | Nous avons besoin d’un point de terminaison URI de requête (moteur) **standard**. |
    | Nom | engine-pip | Choisissez un nom qui identifie votre groupe adresse IP publique dans le groupe de ressources.
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre adresse IP publique.|
    | Resource group | Votre groupe de ressources | Utilisez un groupe de ressources existant ou créez-en un. |
    | Emplacement | *USA Ouest* | Sélectionnez la région qui répond le mieux à vos besoins.
    | | | |

1. Sélectionnez **Créer** pour créer l’adresse IP publique.

1. Pour créer l’adresse IP publique d’ingestion (Gestion des données), suivez les mêmes instructions et sélectionnez 
    * **Référence (SKU)**  : De base
    * **Affectation d'adresses IP** : statique

## <a name="create-virtual-network-and-subnet"></a>Créer un réseau virtuel et un sous-réseau

Pour créer le réseau virtuel et le sous-réseau :

1. Sélectionnez le bouton **+ Créer une ressource** dans le coin supérieur gauche du portail.
1. Recherchez *Réseau virtuel*.
1. Dans **Réseau virtuel**, en bas de l’écran, sélectionnez **Créer**.
1. Dans la fenêtre **Créer un réseau virtuel**, renseignez les informations suivantes.

   ![Formulaire Créer un réseau virtuel](media/vnet-create-cluster-portal/vnet-blade.png)

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre cluster.|
    | Resource group | Votre groupe de ressources | Utilisez un groupe de ressources existant ou créez-en un. |
    | Nom | AzureDataExplorerVnet | Choisissez un nom qui identifie votre réseau virtuel dans le groupe de ressources.
    | Région | *USA Ouest* | Sélectionnez la région qui répond le mieux à vos besoins.
    | | | |

    > [!NOTE]
    > Pour les charges de travail de production, planifiez la taille du sous-réseau en fonction de [Planifier la taille du sous-réseau dans votre réseau virtuel](/azure/data-explorer/vnet-deployment#plan-subnet-size-in-your-vnet).

1. Sélectionnez **Vérifier + créer** pour passer en revue les détails de votre cluster, puis **Créer** pour le provisionner.

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.
1. Accédez au volet **Sous-réseaux**, puis sélectionnez le sous-réseau **par défaut**.
    
    ![Volet sous-réseaux](media/vnet-create-cluster-portal/subnets.png)

1. Dans la fenêtre de sous-réseau **par défaut** :
    1. Sélectionnez votre **groupe de sécurité réseau** dans le menu déroulant. 
    1. Sélectionnez le nom de votre groupe de sécurité réseau, ici **AzureDataExplorerNsg**. 
    1. **Save**

    ![Configurer le sous-réseau](media/vnet-create-cluster-portal/subnet-nsg.png)

## <a name="create-a-cluster"></a>Créer un cluster

Créez un cluster Azure Data Explorer avec un ensemble défini de ressources de calcul et de stockage dans un groupe de ressources Azure comme décrit dans [Créer un cluster](create-cluster-database-portal.md#create-a-cluster).

1. Avant de finaliser la création du cluster, dans la fenêtre **Créer un cluster Azure Data Explorer**, sélectionnez l’onglet **Réseau** pour fournir les détails sur le réseau virtuel avec les ressources créées dans les onglets précédents :

   ![Créer un formulaire de cluster de réseau virtuel](media/vnet-create-cluster-portal/create-cluster-form-vnet.png)

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour vos ressources réseau.|
    | Réseau virtuel | AzureDataExplorerVnet | Choisissez le réseau virtuel que vous avez créé dans les étapes précédentes.
    | Subnet | default | Choisissez sous-réseau que vous avez créé dans les étapes précédentes.
    | Requête d’adresse IP publique | engine-pip | Choisissez l’adresse IP publique que vous avez créée dans les étapes précédentes.
    | Adresse IP publique d’ingestion des données | dm-pip | Choisissez l’adresse IP publique d’ingestion que vous avez créée dans les étapes précédentes.
    | | | |

1. Sélectionnez **Vérifier + créer** pour créer votre cluster.
1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

Pour déployer votre cluster Azure Data Explorer dans votre réseau virtuel, utilisez le modèle Azure Resource Manager [Déployer le cluster Azure Data Explorer dans votre réseau virtuel](https://azure.microsoft.com/resources/templates/101-kusto-vnet/).

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Déployer Azure Data Explorer dans votre réseau virtuel](vnet-create-cluster-portal.md)