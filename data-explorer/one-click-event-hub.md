---
title: Utilisez l’ingestion en un clic pour ingérer des données dans Azure Data Explorer à partir d’Event Hub.
description: Dans cet article, vous allez voir comment ingérer (charger) des données dans Azure Data Explorer à partir d’Event Hub en un seul clic.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 6f7b33941ca38f80249808d14514faa2378c61fb
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714969"
---
# <a name="use-one-click-ingestion-to-create-an-event-hub-data-connection-for-azure-data-explorer"></a>Utiliser l’ingestion en un clic afin de créer une connexion de données Event Hub pour Azure Data Explorer

> [!div class="op_single_selector"]
> * [Portail](ingest-data-event-hub.md)
> * [En un clic](one-click-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md)

L’Explorateur de données Azure offre une ingestion (chargement de données) à partir d’Event Hubs, plateforme de streaming de big data et service d’ingestion d’événements. [Event Hubs](/azure/event-hubs/event-hubs-about) peut traiter des millions d’événements par seconde en quasi-temps réel. Dans cet article, vous allez connecter un hub d’événements à une table dans Azure Data Explorer à l’aide de l’[ingestion en un clic](ingest-data-one-click.md).

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Un cluster et une base de données](create-cluster-database-portal.md).
* [Hub d’événements avec des données pour l’ingestion](ingest-data-event-hub.md#create-an-event-hub).

## <a name="ingest-new-data"></a>Ingérer de nouvelles données

1. Dans le menu de gauche de l’[interface utilisateur web](https://dataexplorer.azure.com/), cliquez avec le bouton droit sur une *base de données* ou une *table*, puis sélectionnez **Ingérer de nouvelles données**. 

:::image type="content" source="media/one-click-event-hub/one-click-ingestion-in-web-ui.png" alt-text="Sélectionner l’ingestion en un clic dans l’interface utilisateur web":::

La fenêtre **Ingérer de nouvelles données** s’ouvre, avec l’onglet **Source** sélectionné.

:::image type="content" source="media/one-click-event-hub/reference-to-event-hub.png" alt-text="Capture d’écran de l’onglet Source dans la fenêtre Ingérer de nouvelles données d’Azure Data Explorer, avec source = Référence à Event Hub":::

1. Le champ **Base de données** est renseigné automatiquement avec votre base de données. Vous pouvez sélectionner une autre base de données dans le menu déroulant.

1. Sous **Table**, sélectionnez **Créer** et entrez un nom pour la nouvelle table, ou utilisez une table existante. 

    > [!NOTE]
    > Les noms de table doivent comprendre entre 1 et 1024 caractères. Vous pouvez utiliser des caractères alphanumériques, des traits d’union et des traits de soulignement. Les caractères spéciaux ne sont pas pris en charge.

1. Sous **Type de source**, sélectionnez **Référence à Event Hub**. La sélection de la connexion de données s’affiche.

## <a name="data-connection"></a>Connexion de données

1. Sous **Connexion de données**, renseignez les champs suivants :

    :::image type="content" source="media/one-click-event-hub/project-details.png" alt-text="Capture d’écran de l’onglet Source avec les champs Détails du projet à renseigner : Ingérer de nouvelles données dans Azure Data Explorer avec Event Hub en un clic":::

    |**Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la connexion de données | *TestDataConnection*  | Nom qui permet d’identifier votre connexion de données.
    | Abonnement |      | ID d’abonnement dans lequel se trouve la ressource de hub d’événements.  |
    | Espace de noms Event Hub |  | Nom unique qui permet d’identifier votre espace de noms. |
    | Event Hub |  | Hub d’événements que vous souhaitez utiliser. |
    | Groupe de consommateurs |  | Groupe de consommateurs défini dans votre hub d’événements. |
    | Format de données | | Les données sont lues à partir du hub d’événements sous forme d’objets [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet). Les formats pris en charge sont CSV, JSON, PSV, SCsv, SOHsv TSV et TSVE. |
    | Propriétés du système d’événements | Sélectionner les propriétés pertinentes | [Propriétés système du hub d’événements](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). S’il existe plusieurs enregistrements par message d’événement, les propriétés système sont ajoutées au premier. Lors de l’ajout des propriétés système, [créez](kusto/management/create-table-command.md) ou [mettez à jour](kusto/management/alter-table-command.md) le schéma de table et le [mappage](kusto/management/mappings.md) pour inclure les propriétés sélectionnées. |

1. Sélectionnez **Modifier le schéma**.

## <a name="schema-tab"></a>Onglet Schéma

Pour plus d’informations sur la mise en correspondance des schémas avec des données au format JSON, consultez [Modifier le schéma](one-click-ingestion-existing-table.md#edit-the-schema).
Pour plus d’informations sur la mise en correspondance des schémas avec des données au format CSV, consultez [Modifier le schéma](one-click-ingestion-new-table.md#edit-the-schema).

:::image type="content" source="media/one-click-event-hub/schema-tab.png" alt-text="Capture d’écran de l’onglet Schéma dans la page Ingérer de nouvelles données d’Azure Data Explorer avec Event Hub en un clic":::

1. Si les données que vous voyez dans la fenêtre d’aperçu sont incomplètes, il est possible que vous ayez besoin de davantage de données pour créer une table avec tous les champs de données nécessaires. Utilisez les commandes suivantes pour récupérer (fetch) de nouvelles données à partir de votre hub d’événements :
    * **Ignorer et récupérer de nouvelles données** : ignore les données présentées et recherche les nouveaux événements.
    * **Récupérer plus de données** : recherche d’autres événements, en plus de ceux déjà trouvés. 
    
    > [!NOTE]
    > Pour que vous puissiez voir un aperçu de vos données, votre hub d’événements doit envoyer des événements.
        
1. Sélectionnez **Démarrer l’ingestion**.

## <a name="complete-data-ingestion"></a>Terminer l’ingestion des données

Dans la fenêtre **Ingestion de données terminée**, toutes les étapes sont signalées par des coches vertes quand l’ingestion des données s’est bien terminée. Les cartes ci-dessous vous permettent d’explorer vos données à l’aide de **requêtes rapides**, d’annuler les modifications apportées à l’aide d’**outils** ou de **superviser** les connexions et les données du hub d’événements.

:::image type="content" source="media/one-click-event-hub/data-ingestion-completed.png" alt-text="Capture de l’écran final du processus d’ingestion dans Azure Data Explorer à partir d’Event Hub avec l’expérience en un clic":::

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans l’interface utilisateur web Azure Data Explorer](web-query-data.md)
* [Écrire des requêtes pour Azure Data Explorer à l’aide du langage de requête Kusto](write-queries.md)
