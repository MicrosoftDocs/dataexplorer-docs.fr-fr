---
title: Visualiser des données Azure Data Explorer avec Grafana
description: Dans cet article, vous allez apprendre à configurer Azure Data Explorer en tant que source de données pour Grafana, puis à visualiser les données d’un exemple de cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: d58e557ecbd7e51e0295bf1a8ece3aaa4996da47
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81493813"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualiser des données Azure Data Explorer dans Grafana

Grafana est une plateforme d’analytique qui vous permet d’interroger et de visualiser des données, puis de créer et partager des tableaux de bord selon vos visualisations. Grafana propose un *plug-in* Azure Data Explorer, qui vous permet de vous connecter à des données et de les visualiser à partir d’Azure Data Explorer. Dans cet article, vous allez apprendre à configurer Azure Data Explorer en tant que source de données pour Grafana, puis à visualiser les données d’un exemple de cluster.

À l’aide de la vidéo suivante, apprenez à utiliser le plug-in Azure Data Explorer de Grafana, à configurer Azure Data Explorer comme source de données pour Grafana, puis à visualiser les données. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Vous pouvez également [configurer la source de données](#configure-the-data-source) et [visualiser les données](#visualize-data) comme expliqué dans l’article ci-dessous.

## <a name="prerequisites"></a>Conditions préalables requises

Vous avez besoin des éléments suivants dans le cadre de cet article :

* [Grafana version 5.3.0 ou ultérieure](https://docs.grafana.org/installation/) pour votre système d’exploitation

* Le [plug-in Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) pour Grafana

* Un cluster qui inclut l’exemple de données StormEvents. Pour plus d’informations, consultez [Démarrage rapide : Créer un cluster et une base de données Explorateur de données Azure](create-cluster-database-portal.md) et [Ingérer des exemples de données dans l’Explorateur de données Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Spécifier des propriétés et tester la connexion

Maintenant que le principal de service est affecté au rôle *observateurs*, vous pouvez spécifier des propriétés dans votre instance de Grafana, puis tester la connexion à Azure Data Explorer.

1. Dans Grafana, dans le menu de gauche, sélectionnez l’icône d’engrenage, puis **Sources de données**.

    ![Sources de données](media/grafana/data-sources.png)

1. Sélectionnez **Ajouter une source de données**.

1. Dans la page **Sources de données/Nouveau**, entrez un nom pour la source de données, puis sélectionnez le type **Source de données Azure Data Explorer**.

    ![Nom et type de la connexion](media/grafana/connection-name-type.png)

1. Entrez le nom de votre cluster au format https://{ClusterName}.{Region{Region}.kusto.windows.net. Entrez les autres valeurs à partir du portail Azure ou de l’interface CLI. Consultez le tableau situé sous l’image suivante pour obtenir un mappage.

    ![Propriétés de connexion](media/grafana/connection-properties.png)

    | Interface utilisateur de Grafana | Portail Azure | Azure CLI |
    | --- | --- | --- |
    | ID d’abonnement | ID D’ABONNEMENT | SubscriptionId |
    | ID client | ID du répertoire | tenant |
    | ID de client | ID de l'application | appId |
    | Clé secrète client | Mot de passe | password |
    | | | |

1. Sélectionnez **Enregistrer et tester**.

    Si le test est réussi, passez à la section suivante. Si vous rencontrez des problèmes, vérifiez les valeurs que vous avez spécifiées dans Grafana, puis vérifiez les étapes précédentes.

## <a name="visualize-data"></a>Visualiser les données

Maintenant que vous avez fini de configurer Azure Data Explorer en tant que source de données pour Grafana, le moment est venu de visualiser les données. Nous allons vous montrer un exemple très simple, mais sachez que les possibilités qui s’offrent à vous sont bien plus grandes. Nous vous recommandons de consulter [Écrire des requêtes pour Azure Data Explorer](write-queries.md) pour obtenir d’autres exemples de requêtes à exécuter sur l’exemple de jeu de données.

1. Dans Grafana, dans le menu de gauche, sélectionnez l’icône plus (+), puis **Tableau de bord**.

    ![Créer un tableau de bord](media/grafana/create-dashboard.png)

1. Sous l’onglet **Ajouter**, sélectionnez **Graphe**.

    ![Ajouter un graphe](media/grafana/add-graph.png)

1. Dans le panneau du graphe, sélectionnez **Titre du panneau**, puis **Modifier**.

    ![Modifier le panneau](media/grafana/edit-panel.png)

1. Au bas du panneau, sélectionnez **Source de données**, puis sélectionnez la source de données que vous avez configurée.

    ![Sélectionnez la source de données](media/grafana/select-data-source.png)

1. Dans le volet de requête, copiez la requête suivante, puis sélectionnez **Exécuter**. La requête compartimente le nombre d’événements par jour pour l’exemple de jeu de données.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Exécuter une requête](media/grafana/run-query.png)

1. Le graphe ne montre aucun résultat car sa portée se limite par défaut aux données des six dernières heures. Dans le menu supérieur, sélectionnez **6 dernières heures**.

    ![Six dernières heures](media/grafana/last-six-hours.png)

1. Spécifiez une plage personnalisée qui couvre l’année 2007, année incluse dans notre exemple de jeu de données StormEvents. Sélectionnez **Appliquer**.

    ![Plage de dates personnalisée](media/grafana/custom-date-range.png)

    Le graphe montre maintenant les données de 2007, compartimentées par jour.

    ![Graphe terminé](media/grafana/finished-graph.png)

1. Dans le menu supérieur, sélectionnez l’icône Enregistrer : ![Icône Enregistrer](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Créer des alertes

1. Dans le tableau de bord de l’Accueil, sélectionnez **Alertes** > **Canaux de notification** pour créer un canal de notification.

    ![créer un canal de notification](media/grafana/create-notification-channel.png)

1. Créez un nouveau **Canal de notification**, puis sélectionnez **Enregistrer**.

    ![Créer un nouveau canal de notification](media/grafana/new-notification-channel-adx.png)

1. Dans le **tableau de bord**, sélectionnez **Modifier** dans la liste déroulante.

    ![sélectionner modifier dans le tableau de bord](media/grafana/edit-panel-4-alert.png)

1. Sélectionnez l’icône représentant une cloche d’alerte pour ouvrir le volet **Alerte**. Sélectionnez **Créer une alerte**. Renseignez les propriétés suivantes dans le volet **Alerte**.

    ![propriétés d’alerte](media/grafana/alert-properties.png)

1. Sélectionnez le bouton **Enregistrer le tableau de bord** pour enregistrer vos modifications.

## <a name="next-steps"></a>Étapes suivantes

* [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)
