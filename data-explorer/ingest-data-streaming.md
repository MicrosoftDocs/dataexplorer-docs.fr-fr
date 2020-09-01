---
title: Configurer l’ingestion de streaming dans votre cluster Azure Data Explorer à l’aide du portail Azure
description: Découvrez comment configurer votre cluster Azure Data Explorer et comment charger des données avec l’ingestion de streaming à l’aide du portail Azure.
author: orspod
ms.author: orspodek
ms.reviewer: alexefro
ms.service: data-explorer
ms.topic: how-to
ms.date: 07/13/2020
ms.openlocfilehash: 417d2b45e53abeba40f099a33880912a9300bc31
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874849"
---
# <a name="configure-streaming-ingestion-on-your-azure-data-explorer-cluster-using-the-azure-portal"></a>Configurer l’ingestion de streaming dans votre cluster Azure Data Explorer à l’aide du portail Azure

> [!div class="op_single_selector"]
> * [Portail](ingest-data-streaming.md)
> * [C#](ingest-data-streaming-csharp.md)

[!INCLUDE [ingest-data-streaming-intro](includes/ingest-data-streaming-intro.md)]

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créer [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Activer l’ingestion de streaming sur votre cluster

### <a name="enable-streaming-ingestion-while-creating-a-new-cluster-in-the-azure-portal"></a>Activer l’ingestion de streaming lors de la création d’un cluster dans le portail Azure

Vous pouvez activer l’ingestion de streaming lors de la création d’un cluster Azure Data Explorer. 

Sous l’onglet **Configurations**, sélectionnez **Ingestion de streaming** > **Activé**.

:::image type="content" source="media/ingest-data-streaming/cluster-creation-enable-streaming.png" alt-text="Activer l’ingestion de streaming lors de la création d’un cluster dans Azure Data Explorer":::

### <a name="enable-streaming-ingestion-on-an-existing-cluster-in-the-azure-portal"></a>Activer l’ingestion de streaming dans un cluster existant à partir du portail Azure

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer. 
1. Dans **Paramètres**, sélectionnez **Configurations**. 
1. Dans le volet **Configurations**, sélectionnez **Activé** pour activer **Ingestion de streaming**.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-on.png" alt-text="Activer l’ingestion de streaming dans Azure Data Explorer":::

> [!WARNING]
> Avant d’activer l’ingestion de streaming, passez en revue les [limitations](#limitations).

## <a name="create-a-target-table-and-define-the-policy-in-the-azure-portal"></a>Créer une table cible et définir la stratégie dans le portail Azure

1. Dans le portail Azure, accédez à votre cluster.
1. Sélectionnez **Requête**.

    :::image type="content" source="media/ingest-data-streaming/cluster-select-query-tab.png" alt-text="Sélection de l’option Requête dans le portail Azure Data Explorer pour activer l’ingestion de streaming":::

1. Pour créer la table qui recevra les données par le biais de l’ingestion de streaming, copiez la commande suivante dans le **volet de requête**, puis sélectionnez **Exécuter**.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    :::image type="content" source="media/ingest-data-streaming/create-table.png" alt-text="Créer une table pour l’ingestion de streaming dans Azure Data Explorer":::

1. Définissez la [stratégie d’ingestion de streaming](kusto/management/streamingingestionpolicy.md) pour la table que vous avez créée ou la base de données qui contient cette table. 
 
    > [!TIP]
    > Une stratégie qui est définie au niveau de la base de données s’applique à toutes les tables existantes et futures de la base de données. 
    
1. Copiez l’une des commandes suivantes dans le **volet de requête**, puis sélectionnez **Exécuter**.

    ```kusto
    .alter table TestTable policy streamingingestion enable
    ```

    or

    ```kusto
    .alter database StreamingTestDb policy streamingingestion enable
    ```

    :::image type="content" source="media/ingest-data-streaming/define-streaming-ingestion-policy.png" alt-text="Définir la stratégie d’ingestion de streaming dans Azure Data Explorer":::

[!INCLUDE [ingest-data-streaming-use](includes/ingest-data-streaming-types.md)]

[!INCLUDE [ingest-data-streaming-disable](includes/ingest-data-streaming-disable.md)]

## <a name="drop-the-streaming-ingestion-policy-in-the-azure-portal"></a>Supprimer la stratégie d’ingestion de streaming dans le portail Azure

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer, puis sélectionnez **Requête**. 
1. Pour supprimer la stratégie d’ingestion de streaming de la table, copiez la commande suivante dans le **volet de requête**, puis sélectionnez **Exécuter**.

    ```Kusto
    .delete table TestTable policy streamingingestion 
    ```

    :::image type="content" source="media/ingest-data-streaming/delete-streaming-ingestion-policy.png" alt-text="Supprimer la stratégie d’ingestion de streaming dans Azure Data Explorer":::

1. Dans **Paramètres**, sélectionnez **Configurations**.
1. Dans le volet **Configurations**, sélectionnez **Désactivé** pour désactiver **Ingestion de streaming**.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/ingest-data-streaming/streaming-ingestion-off.png" alt-text="Désactiver l’ingestion de streaming dans Azure Data Explorer":::

[!INCLUDE [ingest-data-streaming-limitations](includes/ingest-data-streaming-limitations.md)]

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans Azure Data Explorer](web-query-data.md)
