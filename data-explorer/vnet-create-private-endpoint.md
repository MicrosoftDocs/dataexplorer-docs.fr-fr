---
title: Utilisation d’un point de terminaison privé dans votre cluster Azure Data Explorer dans votre réseau virtuel
description: Utilisation d’un point de terminaison privé dans votre cluster Azure Data Explorer dans votre réseau virtuel
author: orspod
ms.author: orspodek
ms.reviewer: elbirnbo
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: 55de6b9560b2b47496c122e6454e1a128dc26428
ms.sourcegitcommit: 9e0289945270db517e173aa10024e0027b173b52
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428912"
---
# <a name="create-a-private-endpoint-in-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Créer un point de terminaison privé dans votre cluster Azure Data Explorer dans votre réseau virtuel

Utilisez un lien privé avec un point de terminaison privé pour accéder de façon sécurisée à votre cluster Azure Data Explorer dans votre réseau virtuel. 

Pour configurer votre [service de liaison privée](https://docs.microsoft.com/azure/private-link/private-link-service-overview), utilisez un point de terminaison privé avec une adresse IP issue de l’espace d’adressage de votre réseau virtuel Azure. Le [point de terminaison privé Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) utilise une adresse IP privée de votre réseau virtuel pour vous connecter de manière privée et sécurisée à Azure Data Explorer. Vous devez également reconfigurer la [configuration DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-dns) sur votre cluster pour vous connecter à l’aide de votre point de terminaison privé. Avec cette configuration, le trafic réseau entre un client de votre réseau privé et le cluster Azure Data Explorer traverse le réseau virtuel et une [liaison privée](https://docs.microsoft.com/azure/private-link/) sur le réseau principal Microsoft, ce qui élimine toute exposition sur le réseau Internet public. Cet article explique comment créer et configurer un point de terminaison privé dans votre cluster pour la requête (moteur) et l’ingestion (gestion des données).

## <a name="prerequisites"></a>Prérequis

* Créez un [cluster Azure Data Explorer dans votre réseau virtuel](https://docs.microsoft.com/azure/data-explorer/vnet-create-cluster-portal).
* [Désactivez les stratégies réseau](https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy) comme les groupes de sécurité réseau (NSG). Ces groupes ne sont pas pris en charge pour les points de terminaison privés.

## <a name="create-private-link-service"></a>Créer un service de liaison privée

Pour établir une liaison sécurisée à tous les services dans votre cluster, vous devez créer le [service de liaison privée](https://docs.microsoft.com/azure/private-link/private-link-service-overview) deux fois : une fois pour la requête (moteur) et une fois pour l’ingestion (gestion des données).

1. Sélectionnez le bouton **+ Créer une ressource** dans le coin supérieur gauche du portail.
1. Recherchez *Service de liaison privée*.
1. Sous **Service de liaison privée**, sélectionnez **Créer**.

    :::image type="content" source="media/vnet-create-private-endpoint/create-service.gif" alt-text="Image GIF montrant les trois premières étapes de création d’un service de liaison privée dans le portail Azure Data Explorer":::

1. Dans le volet **Créer un service de liaison privée**, renseignez les champs suivants :

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-basics.png" alt-text="Onglet 1 dans Créer un service de liaison privée – Bases":::

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure qui contient votre cluster de réseau virtuel.|
    | Resource group | Votre groupe de ressources | Sélectionnez le groupe de ressources qui contient votre cluster de réseau virtuel. |
    | Nom | AzureDataExplorerPLS | Choisissez un nom qui identifie votre service de liaison privée dans le groupe de ressources. |
    | Région | Identique au réseau virtuel | Sélectionnez la région qui correspond à la région de votre réseau virtuel. |

1. Dans le volet **Paramètres sortants**, renseignez les champs suivants :

    :::image type="content" source="media/vnet-create-private-endpoint/private-link-outbound.png" alt-text="Onglet 2 de liaison privée – Paramètres sortants":::

    |**Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Load Balancer | Votre moteur ou équilibreur de charge de *gestion des données* | Sélectionnez l’équilibreur de charge créé pour votre moteur de cluster, équilibreur de charge qui pointe vers l’adresse IP publique de votre moteur.  Le nom du moteur d’équilibrage de charge sera au format suivant : kucompute-{clustername}-elb <br> *Le nom de gestion des données de l’équilibreur de charge sera au format suivant : kudatamgmt-{clustername}-elb*|
    | Adresse IP front-end de l’équilibreur de charge | Adresse IP publique de gestion de données ou de votre moteur. | Sélectionnez l’adresse IP publique de l’équilibreur de charge. |
    | Sous-réseau NAT source | Sous-réseau du cluster | Votre sous-réseau où le cluster est déployé.
    
1. Dans le volet **Sécurité d’accès**, choisissez les utilisateurs qui peuvent demander l’accès à votre service de liaison privée.
1. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre service de liaison privée. Sélectionnez **Créer** pour créer le service de liaison privée.
1. Une fois le service de liaison privée créé, ouvrez la ressource et enregistrez l’alias de liaison privée pour l’étape suivante, **Créer un point de terminaison privé**. L’exemple d’alias est : *AzureDataExplorerPLS.111-222-333.westus.azure.privatelinkservice*

## <a name="create-private-endpoint"></a>Créer un point de terminaison privé

Pour établir une liaison sécurisée à tous les services dans votre cluster, vous devez créer le [point de terminaison privé](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) deux fois : une fois pour la requête (moteur) et une fois pour l’ingestion (gestion des données).

1. Sélectionnez le bouton **+ Créer une ressource** dans le coin supérieur gauche du portail.
1. Recherchez *Point de terminaison privé*.
1. Sous **Point de terminaison privé**, sélectionnez **Créer**.
1. Dans le volet **Créer un point de terminaison privé**, renseignez les champs suivants :

    :::image type="content" source="media/vnet-create-private-endpoint/step-one-basics.png" alt-text="Étape 1 du formulaire de création d’un point de terminaison privé – Bases":::

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre point de terminaison privé.|
    | Resource group | Votre groupe de ressources | Utilisez un groupe de ressources existant ou créez-en un. |
    | Nom | AzureDataExplorerPE | Choisissez un nom qui identifie votre réseau virtuel dans le groupe de ressources.
    | Région | *USA Ouest* | Sélectionnez la région qui répond le mieux à vos besoins.
    
1. Dans le volet **Ressource**, renseignez les champs suivants :

    :::image type="content" source="media/vnet-create-private-endpoint/step-two-resource.png" alt-text="Étape 2 du formulaire de création d’un réseau virtuel – Ressource":::

    **Paramètre** | **Valeur**
    |---|---|
    | Méthode de connexion | Alias de votre service de liaison privée |
    | ID de ressource ou alias | Alias de votre service de liaison privée de moteur. L’exemple d’alias est : *AzureDataExplorerPLS.111-222-333.westus.azure.privatelinkservice*  |
    
1. Sélectionnez **Vérifier + créer** pour passer en revue la configuration de votre point de terminaison privé et **créer** le service de point de terminaison privé.
1. Pour créer le point de terminaison privé pour l’ingestion (gestion des données), suivez les mêmes instructions avec la modification suivante :
    1. Dans le volet **Ressource**, choisissez l’alias de votre service de liaison privée d’ingestion (gestion des données).

> [!NOTE]
> Vous pouvez vous connecter au service de liaison privée à partir de plusieurs points de terminaison privés.

## <a name="approve-your-private-endpoint"></a>Approuver votre point de terminaison privé

> [!NOTE]
> Si vous avez choisi l’option *approuver automatiquement* dans le volet **Sécurité d’accès** lors de la création du service de liaison privée, cette étape n’est pas obligatoire.

1. Dans votre service de liaison privée, choisissez **Connexions de points de terminaison privés** sous les paramètres.
1. Choisissez votre point de terminaison privé dans la liste des connexions et sélectionnez **Approuver**.

:::image type="content" source="media/vnet-create-private-endpoint/private-link-approve.png" alt-text="Étape d’approbation pour créer un point de terminaison privé"::: 

## <a name="set-dns-configuration"></a>Définir la configuration DNS

Lorsque vous déployez un cluster Azure Data Explorer dans votre réseau virtuel, nous mettons à jour l’[entrée DNS](https://docs.microsoft.com/azure/private-link/private-endpoint-dns) pour pointer vers le nom canonique avec *privatelink* entre le nom d’enregistrement et le nom d’hôte de la zone. Cette entrée est mise à jour à la fois pour le moteur et l’ingestion (gestion des données). 

Par exemple, si le nom DNS de votre moteur est myadx.westus.kusto.windows.net, la résolution de noms est la suivante :

* **nom** : myadx.westus.kusto.windows.net   <br> **type** : CNAME   <br> **valeur** : myadx.privatelink.westus.kusto.windows.net
* **nom** : myadx.privatelink.westus.kusto.windows.net   <br> **type** : A   <br> **value** : 40.122.110.154
    > [!NOTE]
    > Cette valeur correspond à l’adresse IP publique de requête (moteur) que vous avez fournie quand vous avez créé le cluster.

Configurez un serveur DNS privé ou une zone DNS privée Azure. À des fins de tests, vous pouvez modifier l’entrée d’hôte de votre ordinateur de test.

Créez la zone DNS suivante : **privatelink.region.kusto.windows.net**. La zone DNS de cet exemple est : *privatelink.westus.kusto.windows.net*. Inscrivez l’enregistrement pour votre moteur avec un enregistrement A et l’adresse IP du point de terminaison privé.

Voici des exemples de résolution des noms :

* **nom** : myadx.westus.kusto.windows.net   <br>**type**: CNAME   <br>**valeur** : myadx.privatelink.westus.kusto.windows.net
* **nom** : myadx.privatelink.westus.kusto.windows.net   <br>**type** : A   <br>**value** : 10.3.0.9
    > [!NOTE]
    > Cette valeur est l’adresse IP de votre point de terminaison privé. Vous avez déjà connecté l’adresse IP au service de liaison privée de requête (moteur).

Après avoir défini cette configuration DNS, vous pouvez atteindre la requête (moteur) à l’intérieur de votre réseau virtuel de façon privée avec l’URL suivante : myadx.region.kusto.windows.net.

Pour atteindre l’ingestion (gestion des données) de façon privée, inscrivez l’enregistrement pour votre ingestion (gestion des données) avec un enregistrement A et l’adresse IP du point de terminaison privé d’ingestion.

## <a name="next-steps"></a>Étapes suivantes

* [Déployer un cluster Azure Data Explorer dans votre réseau virtuel](vnet-deployment.md)
* [Résoudre les problèmes d’accès, d’ingestion et de fonctionnement de votre cluster Azure Data Explorer dans votre réseau virtuel](vnet-deploy-troubleshoot.md)
