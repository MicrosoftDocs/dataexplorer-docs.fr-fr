---
title: Créer un abonnement Event Grid dans votre compte de stockage - Azure Data Explorer
description: Cet article décrit comment créer un abonnement Event Grid dans votre compte de stockage dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: kedamari
ms.service: data-explorer
ms.topic: reference
ms.date: 10/05/2020
ms.openlocfilehash: 51d5c48fbcd2373ed5cdd1973cc9e53abb26b2de
ms.sourcegitcommit: 58588ba8d1fc5a6adebdce2b556db5bc542e38d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92099484"
---
# <a name="manually-create-resources-for-event-grid-ingestion"></a>Créer manuellement des ressources pour l’ingestion Event Grid

> [!div class="op_single_selector"]
> * [Portail](ingest-data-event-grid.md)
> * [Portail - Créer des ressources manuellement](ingest-data-event-grid-manual.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)

Azure Data Explorer offre une ingestion continue depuis Stockage Azure (Stockage Blob Azure et Azure Data Lake Storage Gen2) en utilisant un [pipeline d’ingestion Event Grid](ingest-data-event-grid-overview.md). Dans le pipeline d’ingestion Event Grid, un service Azure Event Grid route les événements de création ou de renommage d’objets blob depuis un compte de stockage vers Azure Data Explorer via un hub d’événements Azure.

Dans cet article, vous découvrez comment créer manuellement les ressources nécessaires à l’ingestion Event Grid : abonnement Event Grid, espace de noms Event Hub et hub d’événements. La création de l’espace de noms Event Hub et du hub d’événements est décrite dans les [prérequis](#prerequisites). Pour utiliser la création automatique de ces ressources lors de la définition de l’ingestion Event Grid, consultez [Créer une connexion de données Event Grid dans Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Créez un [compte Azure gratuit](https://azure.microsoft.com/free/).
* [Un compte de stockage](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
    * L’abonnement aux notifications Event Grid peut être défini sur des comptes de stockage Azure pour `BlobStorage`, `StorageV2` ou [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction).
* [Un espace de noms Event Hub et un hub d’événements](/azure/event-hubs/event-hubs-create)

> [!NOTE]
> Pour des performances optimales, créez toutes les ressources dans la même région que le cluster Azure Data Explorer.

## <a name="create-an-event-grid-subscription"></a>Créer un abonnement Event Grid
 
1. Dans le portail Azure, accédez à votre compte de stockage.
1. Dans le menu de gauche, sélectionnez **Événements** > **Abonnement à un événement**.

     :::image type="content" source="media/eventgrid/create-event-grid-subscription-1.png" alt-text="Créer un abonnement Event Grid":::

1. Dans la fenêtre **Créer un abonnement aux événements** sous l’onglet **De base**, spécifiez les valeurs suivantes :

    :::image type="content" source="media/eventgrid/create-event-grid-subscription-2.png" alt-text="Créer un abonnement Event Grid":::

    |**Paramètre** | **Valeur suggérée** | **Description du champ**|
    |---|---|---|
    | Nom | *test-grid-connection* | Nom de l’abonnement Event Grid que vous voulez créer.|
    | Schéma d’événement | *Schéma Event Grid* | Le schéma qui doit être utilisé pour la grille d’événements. |
    | Type de rubrique | *Compte de stockage* | Type de rubrique Event Grid. Rempli automatiquement.|
    | Ressource source | *gridteststorage1* | nom de votre compte de stockage. Rempli automatiquement.|
    | Nom de la rubrique système | *gridteststorage1...* | Rubrique système où Stockage Azure publie les événements. Cette rubrique système transfère ensuite l’événement à un abonné qui reçoit et traite des événements. Rempli automatiquement.|
    | Filtrer les types d’événements | *Objet blob créé* | De quels événements spécifiques être notifié. Lors de la création de l’abonnement, sélectionnez un des types actuellement pris en charge : Microsoft.Storage.BlobCreated. ou Microsoft.Storage.BlobRenamed|

1. Dans **DÉTAILS DES POINTS DE TERMINAISON**, sélectionnez **Event Hubs**.

    :::image type="content" source="media/eventgrid/endpoint-details.png" alt-text="Créer un abonnement Event Grid":::

1. Cliquez sur **Sélectionner un point de terminaison** et spécifiez le hub d’événements que vous avez créé, par exemple *test-hub*.
    
1. Sélectionnez l’onglet **Filtres** si vous voulez suivre des sujets spécifiques. Définissez les filtres pour les notifications comme suit :
   
    :::image type="content" source="media/eventgrid/filters-tab.png" alt-text="Créer un abonnement Event Grid":::

   1. Sélectionnez **Activer le filtrage d’objet**.
   1. Le champ **Le sujet commence par** est le préfixe *littéral* du sujet. Comme le modèle appliqué est *startswith*, il peut englober plusieurs conteneurs, dossiers ou objets blob. Les caractères génériques ne sont pas autorisés.
       * Pour définir un filtre sur le conteneur d’objets blob, définissez le champ comme suit : *`/blobServices/default/containers/[container prefix]`* .
       * Pour définir un filtre sur un préfixe d’objet blob (ou un dossier dans Azure Data Lake Gen2), définissez le champ comme suit : *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
   1. Le champ **Le sujet se termine par** est le suffixe *littéral* de l’objet blob. Les caractères génériques ne sont pas autorisés.
   1. Le champ **Correspondance sensible à la casse** indique si les filtres de préfixe et de suffixe sont sensibles à la casse.

    Pour plus d’informations sur le filtrage des événements, consultez [Événements du stockage d’objets blob](/azure/storage/blobs/storage-blob-event-overview#filtering-events).

1. Sélectionnez **Créer**

## <a name="next-steps"></a>Étapes suivantes

* Poursuivez l’installation et créez une connexion d’ingestion de données à Azure Data Explorer via le portail Azure : [Créer une connexion de données Event Grid dans Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer).

* Si vous ne prévoyez pas de poursuivre l’ingestion Event Grid en utilisant les ressources que vous avez créées et que vous ne voulez plus les utiliser, [nettoyez les ressources](ingest-data-event-grid.md#clean-up-resources) pour éviter les coûts.
