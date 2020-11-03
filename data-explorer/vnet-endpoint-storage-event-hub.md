---
title: Créer un point de terminaison privé ou de service pour des ressources utilisées par des connexions de données, telles qu’Event Hub et Stockage Azure
description: Découvrez comment activer un point de terminaison privé ou de service pour des ressources utilisées par des connexions de données, telles qu’Event Hub et Stockage.
author: orspod
ms.author: orspodek
ms.reviewer: gunjand
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: 43f7705170228afa3d3f5e31086d40cea73c62db
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906192"
---
# <a name="create-a-private-or-service-endpoint-to-event-hub-and-azure-storage"></a>Créer un point de terminaison privé ou de service vers Event Hub et Stockage Azure

Le [réseau virtuel Azure](/azure/virtual-network/virtual-networks-overview) permet à de nombreux types de ressources Azure de communiquer de manière sécurisée les uns avec les autres. [Azure Private Link](/azure/private-link/) vous permet d’accéder aux services Azure ainsi qu’aux services de partenaires ou de clients hébergés par Azure par le biais d’un point de terminaison privé dans votre réseau virtuel. Un [point de terminaison privé](/azure/private-link/private-endpoint-overview) utilise une adresse IP de l’espace d’adressage de votre réseau virtuel afin que le service Azure établisse une connexion sécurisée entre Azure Data Explorer et des services Azure tels que Stockage Azure et Event Hub. Azure Data Explorer accède au point de terminaison privé des comptes de stockage ou Event Hubs par le biais du réseau principal Microsoft, et toutes les communications, telles que l’exportation de données, les tables externes et l’ingestion de données, sont effectuées par le biais de l’adresse IP privée. 

Contrairement à un point de terminaison privé, un [point de terminaison de service](/azure/virtual-network/virtual-network-service-endpoints-overview) reste une adresse IP routable publiquement. Un point de terminaison de service de réseau virtuel fournit une connectivité sécurisée et directe aux services Azure sur une route optimisée du réseau principal Azure. 

Cet article explique comment créer une connexion entre Azure Data Explorer et [Event Hub](ingest-data-event-hub-overview.md) ou [Stockage Azure](/azure/storage/).

## <a name="prerequisites"></a>Conditions préalables requises

* [Réseau virtuel Azure](/azure/virtual-network/virtual-networks-overview)
* [Sous-réseau Azure Data Explorer](vnet-deployment.md)

## <a name="private-endpoint"></a>Point de terminaison privé

Azure [Private Endpoint](/azure/private-link/private-endpoint-overview) est une interface réseau qui vous permet de vous connecter de façon privée et sécurisée à un service basé sur Azure [Private Link](/azure/private-link/). Private Endpoint utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel. 
 
# <a name="azure-storage---private-endpoint"></a>[Stockage Azure - Point de terminaison privé](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>Autoriser l’accès à un compte Stockage Azure à partir de sous-réseaux Azure Data Explorer à l’aide d’un point de terminaison privé

Pour obtenir un tutoriel sur la création d’un point de terminaison privé dans votre compte Stockage Azure, consultez [Tutoriel : Se connecter à un compte de stockage en utilisant un point de terminaison privé Azure](/azure/private-link/tutorial-private-endpoint-storage-portal).

Dans ce tutoriel, sélectionnez le réseau virtuel où se trouve le sous-réseau Azure Data Explorer, et le sous-réseau Azure Data Explorer.

# <a name="event-hub---private-endpoint"></a>[Event Hub - Point de terminaison privé](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-private-endpoint"></a>Autoriser l’accès à un hub d’événements Azure à partir de sous-réseaux Azure Data Explorer à l’aide d’un point de terminaison privé

Pour obtenir un tutoriel sur la création d’un point de terminaison privé dans votre hub d’événements, consultez [Ajouter un point de terminaison privé avec le portail Azure](/azure/event-hubs/private-link-service#add-a-private-endpoint-using-azure-portal).

Dans ce tutoriel, sélectionnez le réseau virtuel où se trouve le sous-réseau Azure Data Explorer, et le sous-réseau Azure Data Explorer.

---

## <a name="service-endpoint"></a>Point de terminaison de service

Un [point de terminaison de service](/azure/virtual-network/virtual-network-service-endpoints-overview) de réseau virtuel fournit une connectivité sécurisée et directe aux services Azure sur une route optimisée du réseau principal Azure. Les points de terminaison permettent de sécuriser vos ressources critiques du service Azure pour vos réseaux virtuels uniquement. 

# <a name="azure-storage---service-endpoint"></a>[Stockage Azure - Point de terminaison de service](#tab/storage-account)

### <a name="allow-access-to-azure-storage-account-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>Autoriser l’accès à un compte Stockage Azure à partir de sous-réseaux Azure Data Explorer à l’aide d’un point de terminaison de service

Cette section montre comment utiliser le portail Azure pour ajouter un point de terminaison de service de réseau virtuel. Pour limiter l’accès, intégrez le point de terminaison de service de réseau virtuel pour ce compte Stockage Azure.

### <a name="add-a-virtual-network"></a>Ajouter un réseau virtuel 

1. Accédez au compte de stockage que vous voulez sécuriser.
1. Dans le menu de gauche, sélectionnez **Pare-feux et réseaux virtuels**.
1. Activez l’accès à partir de **Réseaux sélectionnés**.
1. Sous **Réseaux virtuels** , sélectionnez **+ Ajouter un réseau virtuel existant**. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-existing-vnet.png" alt-text="Ajouter une connexion de réseau virtuel existante Stockage Azure à Azure Data Explorer":::

### <a name="add-networks-pane"></a>Volet Ajouter des réseaux

:::image type="content" source="media/vnet-private-link-storage-event-hub/storage-add-networks.png" alt-text="Ajouter un réseau virtuel à un compte Stockage Azure pour se connecter à Azure Data Explorer":::

1. Dans le volet de droite **Ajouter des réseaux** , sélectionnez votre abonnement Azure.

1. Sélectionnez le réseau virtuel dans la liste des réseaux virtuels, puis choisissez le sous-réseau. 

    > [!NOTE]
    > Activez le point de terminaison de service avant d’ajouter le réseau virtuel à la liste. Si le point de terminaison de service n’est pas activé, le portail vous invite à l’activer.
    
1. Sélectionnez **Ajouter**.

### <a name="save-and-verify-virtual-network-settings"></a>Enregistrer et vérifier les paramètres de réseau virtuel

1. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer les paramètres. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/storage-virtual-network.png" alt-text="Réseau virtuel pour connecter le compte de stockage à Azure Data Explorer":::

    Patientez quelques minutes jusqu’à ce que la confirmation s’affiche dans les notifications du portail.

# <a name="event-hub---service-endpoint"></a>[Event Hub - Point de terminaison de service](#tab/event-hub)

### <a name="allow-access-to-azure-event-hub-from-azure-data-explorer-subnets-using-a-service-endpoint"></a>Autoriser l’accès à un hub d’événements Azure à partir de sous-réseaux Azure Data Explorer à l’aide d’un point de terminaison de service

> [!IMPORTANT]
> Les réseaux virtuels sont pris en charge dans les niveaux **standard** et **dédié** d’Event Hubs, et ne sont pas pris en charge dans le niveau de base. 

### <a name="add-a-virtual-network"></a>Ajouter un réseau virtuel

1. Dans le portail Azure, accédez à l’ **Espace de noms Event Hubs** que vous souhaitez sécuriser.
1. Dans le menu de gauche, sélectionnez **Réseau**. Cet onglet est visible uniquement dans un espace de noms **standard** ou **dédié**.
1. Sélectionnez l’onglet **Pare-feux et réseaux virtuels**.

    :::image type="content" source="media/vnet-private-link-storage-event-hub/networking.png" alt-text="Réseau dans Event Hub":::

1. Activez l’accès à partir de **Réseaux sélectionnés**.
1. Sous **Réseaux virtuels** , sélectionnez **+ Ajouter un réseau virtuel existant**. 

    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-add-existing-vnet.png" alt-text="Ajouter un réseau virtuel existant dans Azure Data Explorer":::

### <a name="add-networks-pane"></a>Volet Ajouter des réseaux

:::image type="content" source="media/vnet-private-link-storage-event-hub/add-networks.png" alt-text="Ajouter des champs de réseaux pour connecter un réseau virtuel à Azure Data Explorer":::  

1. Dans le volet de droite **Ajouter des réseaux** , sélectionnez votre abonnement Azure.

1. Sélectionnez le réseau virtuel dans la liste des réseaux virtuels, puis choisissez le sous-réseau. Vous devez activer le point de terminaison de service avant d’ajouter le réseau virtuel à la liste. 
    > [!NOTE]
    > Activez le point de terminaison de service avant d’ajouter le réseau virtuel à la liste. Si le point de terminaison de service n’est pas activé, le portail vous invite à l’activer.
1. Sélectionnez **Ajouter**.

### <a name="save-and-verify-virtual-network-settings"></a>Enregistrer et vérifier les paramètres de réseau virtuel

1. Sélectionnez **Enregistrer** dans la barre d’outils pour enregistrer les paramètres. Patientez quelques minutes jusqu’à ce que la confirmation s’affiche dans les notifications du portail.
    
    :::image type="content" source="media/vnet-private-link-storage-event-hub/event-hub-firewalls-and-vnet.png" alt-text="Ajouter un réseau virtuel et un sous-réseau dans Event Hub pour se connecter à Azure Data Explorer"::: 

---

## <a name="next-steps"></a>Étapes suivantes

* [Exporter des données dans le stockage](kusto/management/data-export/export-data-to-storage.md)
* [Ingérer des données Event Hub dans Azure Data Explorer](ingest-data-event-hub.md)
