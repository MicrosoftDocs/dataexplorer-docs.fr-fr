---
title: Utiliser l’ingestion de streaming pour ingérer des données dans Azure Data Explorer
description: Découvrez comment ingérer (charger) des données dans Azure Data Explorer en utilisant l’ingestion de streaming.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: bda9afdda9d922df6188a3b9170aa4d4926f8ca3
ms.sourcegitcommit: 2126c5176df272d149896ac5ef7a7136f12dc3f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2020
ms.locfileid: "86280420"
---
# <a name="streaming-ingestion-preview"></a>Ingestion de streaming (préversion)

Utilisez l’ingestion de streaming lorsque vous avez besoin d’une faible latence avec une durée d’ingestion de moins de 10 secondes pour des volumes de données variés. Elle est utilisée pour optimiser le traitement opérationnel de nombreuses tables, dans une ou plusieurs bases de données où le flux de données dans chaque table est relativement faible (peu d’enregistrements par seconde), mais le volume global d’ingestion de données est élevé (des milliers d’enregistrements par seconde). 

Utilisez l’ingestion en bloc à la place de l’ingestion en streaming quand la quantité de données dépasse 4 Go par heure et par table. Consultez [Vue d’ensemble de l’ingestion de données](ingest-data-overview.md) pour en apprendre plus sur les différentes méthodes d’ingestion.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Connectez-vous à l’[interface utilisateur web](https://dataexplorer.azure.com/).
* Créer [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md)

## <a name="enable-streaming-ingestion-on-your-cluster"></a>Activer l’ingestion de streaming sur votre cluster

> [!WARNING]
> Examinez les [limitations](#limitations) avant d’activer l’ingestion de streaming.

1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer. Dans **Paramètres**, sélectionnez **Configurations**. 
1. Dans le volet **Configurations**, sélectionnez **Activé** pour activer **Ingestion de streaming**.
1. Sélectionnez **Enregistrer**.
 
    ![Ingestion de streaming activée](media/ingest-data-streaming/streaming-ingestion-on.png)
 
1. Dans l’[interface utilisateur web](https://dataexplorer.azure.com/), définissez la [stratégie d’ingestion de streaming](kusto/management/streamingingestionpolicy.md) sur les tables et bases de données qui recevront les données de streaming. 

    > [!NOTE]
    > * Si la stratégie est définie au niveau de la base de données, toutes les tables de la base de données sont activées pour l’ingestion de streaming.
    > * La stratégie appliquée ne peut faire référence qu’à des données nouvellement ingérées, et non à d’autres tables de la base de données.

## <a name="use-streaming-ingestion-to-ingest-data-to-your-cluster"></a>Utiliser l’ingestion de streaming pour ingérer des données dans votre cluster

Il existe deux types d’ingestion de streaming pris en charge :

* [**Event Hub**](ingest-data-event-hub.md), qui est utilisé comme source de données.
* Une **ingestion personnalisée** vous demande d’écrire une application qui utilise l’une des [bibliothèques clientes](kusto/api/client-libraries.md) Azure Data Explorer. Consultez un [exemple d’ingestion de streaming](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client/StreamingIngestionSample) pour voir un exemple d’application.

### <a name="choose-the-appropriate-streaming-ingestion-type"></a>Choisir le type d’ingestion de streaming approprié

|   |Event Hub  |Ingestion personnalisée  |
|---------|---------|---------|
|Délai de données entre le lancement de l’ingestion et le moment où les données sont disponibles pour une requête   |    Délai plus long     |   Délai plus court      |
|Surcharge de développement    |   Installation rapide et facile, aucune surcharge de développement    |   Surcharge de développement élevée pour l’application afin de gérer les erreurs et d’assurer la cohérence des données     |

## <a name="disable-streaming-ingestion-on-your-cluster"></a>Désactiver l’ingestion de streaming sur votre cluster

> [!WARNING]
> La désactivation de l’ingestion de streaming peut prendre plusieurs heures.

1. Supprimez la [stratégie d’ingestion de streaming](kusto/management/streamingingestionpolicy.md) de toutes les tables et bases de données appropriées. La suppression de la stratégie d’ingestion de streaming déclenche le déplacement des données d’ingestion de streaming du stockage initial vers le stockage permanent dans le stockage de colonnes (étendues ou partitions). Le déplacement des données peut durer de quelques secondes à quelques heures, en fonction de la quantité de données qui se trouvent dans le stockage initial et de la façon dont le processeur et la mémoire sont utilisés par le cluster.
1. Dans le portail Azure, accédez à votre cluster Azure Data Explorer. Dans **Paramètres**, sélectionnez **Configurations**.
1. Dans le volet **Configurations**, sélectionnez **Désactivé** pour désactiver **Ingestion de streaming**.
1. Sélectionnez **Enregistrer**.

    ![Ingestion de streaming désactivée](media/ingest-data-streaming/streaming-ingestion-off.png)

## <a name="limitations"></a>Limites

* Les [curseurs de base de données](kusto/management/databasecursor.md) ne sont pas pris en charge pour une base de données si une [stratégie d’ingestion en streaming](kusto/management/streamingingestionpolicy.md) est définie et activée pour la base de données elle-même ou l’une de ses tables. Dans ce cas, l’exportation continue n’est pas prise en charge et la stratégie de mise à jour est limitée à une requête de la table source uniquement.
* Le [mappage de données](kusto/management/mappings.md) doit être [précréé](kusto/management/create-ingestion-mapping-command.md) pour être utilisée dans une ingestion en streaming. Les demandes d’ingestion en streaming individuelles ne prennent pas en charge les mappages de données inline.
* La capacité et les performances d’ingestion de streaming évoluent avec l’augmentation des tailles de cluster et de machine virtuelle. Le nombre de demandes d’ingestion simultanées est limité à six par cœur. Par exemple, pour les références SKU 16 cœurs telles que D14 et L16, la charge maximale prise en charge est de 96 demandes d’ingestion simultanées. Pour les références SKU 2 cœurs telles que D11, la charge maximale prise en charge est de 12 demandes d’ingestion simultanées.
* La taille limite des données par demande d’ingestion en streaming est de 4 Mo.
* Les mises à jour de schéma, telles que la création et la modification de tables et de mappages d’ingestion, peuvent prendre jusqu’à cinq minutes pour le service d’ingestion de streaming. Pour plus d’informations, consultez [Ingestion de streaming et changements de schéma](kusto/management/data-ingestion/streaming-ingestion-schema-changes.md).
* L’activation de l’ingestion de streaming sur un cluster, même lorsque les données ne sont pas ingérées via streaming, utilise une partie du disque SSD local des machines du cluster pour les données d’ingestion de streaming et réduit le stockage disponible pour le cache à chaud.
* Les [étiquettes d’étendue](kusto/management/extents-overview.md#extent-tagging) ne peuvent pas être définies dans les données d’ingestion de streaming.

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans Azure Data Explorer](web-query-data.md)
