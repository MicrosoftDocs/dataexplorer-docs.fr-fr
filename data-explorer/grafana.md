---
title: Visualiser des données Azure Data Explorer avec Grafana
description: Dans cet article, vous allez apprendre à configurer Azure Data Explorer en tant que source de données pour Grafana, puis à visualiser les données d’un exemple de cluster.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: 08093fd06fed1facc1d8e55d98785abb952632c8
ms.sourcegitcommit: 95527c793eb873f0135c4f0e9a2f661ca55305e3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90534054"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualiser des données Azure Data Explorer dans Grafana

Grafana est une plateforme d’analytique qui vous permet d’interroger et de visualiser des données, puis de créer et partager des tableaux de bord selon vos visualisations. Grafana propose un *plug-in* Azure Data Explorer, qui vous permet de vous connecter à des données et de les visualiser à partir d’Azure Data Explorer. Dans cet article, vous allez apprendre à configurer Azure Data Explorer en tant que source de données pour Grafana, puis à visualiser les données d’un exemple de cluster.

À l’aide de la vidéo suivante, apprenez à utiliser le plug-in Azure Data Explorer de Grafana, à configurer Azure Data Explorer comme source de données pour Grafana, puis à visualiser les données. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Au lieu de cela, vous pouvez [configurer la source de données](#configure-the-data-source) et [visualiser les données](#visualize-data) comme expliqué dans l’article ci-dessous.

## <a name="prerequisites"></a>Conditions préalables requises

Vous avez besoin des éléments suivants dans le cadre de cet article :

* [Grafana version 5.3.0 ou ultérieure](https://docs.grafana.org/installation/) pour votre système d’exploitation

* Le [plug-in Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) pour Grafana. La version 3.0.5 ou ultérieure du plug-in est requise pour utiliser le générateur de requêtes Grafana

* Un cluster qui inclut l’exemple de données StormEvents. Pour plus d’informations, consultez [Démarrage rapide : Créer un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md) et [Ingérer des exemples de données dans Azure Data Explorer](ingest-sample-data.md).

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
    | Clé secrète client | Mot de passe | mot de passe |
    | | | |

1. Sélectionnez **Enregistrer et tester**.

    Si le test est réussi, passez à la section suivante. Si vous rencontrez des problèmes, vérifiez les valeurs que vous avez spécifiées dans Grafana, puis vérifiez les étapes précédentes.

## <a name="visualize-data"></a>Visualiser les données

Maintenant que vous avez fini de configurer Azure Data Explorer en tant que source de données pour Grafana, le moment est venu de visualiser les données. Nous allons voir un exemple de base utilisant à la fois le mode générateur de requêtes et le mode brut de l’éditeur de requête. Nous vous recommandons de consulter [Écrire des requêtes pour Azure Data Explorer](write-queries.md) pour obtenir d’autres exemples de requêtes à exécuter sur l’exemple de jeu de données.

1. Dans Grafana, dans le menu de gauche, sélectionnez l’icône plus (+), puis **Tableau de bord**.

    ![Créer un tableau de bord](media/grafana/create-dashboard.png)

1. Sous l’onglet **Add**, sélectionnez **Add new panel**.

    ![Ajouter un graphe](media/grafana/add-graph.png)

1. Dans le panneau du graphe, sélectionnez **Titre du panneau**, puis **Modifier**.

    ![Modifier le panneau](media/grafana/edit-panel.png)

1. Au bas du panneau, sélectionnez **Source de données**, puis sélectionnez la source de données que vous avez configurée.

    ![Sélectionnez la source de données](media/grafana/select-data-source.png)

### <a name="query-builder-mode"></a>Mode générateur de requêtes

L’éditeur de requête a deux modes : le mode générateur de requêtes et le mode brut. Utilisez le mode générateur de requêtes pour définir votre requête.

1. Sous la source de données, sélectionnez **Database** et choisissez votre base de données dans la liste déroulante. 
1. Sélectionnez **From** et choisissez votre table dans la liste déroulante.

    :::image type="content" source="media/grafana/query-builder-from-table.png" alt-text="Sélectionner une table dans le générateur de requêtes":::    

1. Une fois la table définie, filtrez les données, sélectionnez les valeurs à présenter et définissez le regroupement de ces valeurs.

    **Filter**
    1. Cliquez sur **+** à droite de **Where (filter)** pour sélectionner dans la liste déroulante une ou plusieurs colonnes de votre table. 
    1. Pour chaque filtre, définissez la ou les valeurs à l’aide de l’opérateur applicable. 
    Cette sélection s’apparente à l’utilisation de l’[opérateur where](kusto/query/whereoperator.md) dans le langage de requête Kusto.

    **Sélection des valeurs**
    1. Cliquez sur **+** à droite de **value columns** pour sélectionner dans la liste déroulante les colonnes de valeurs qui seront affichées dans le panneau.
    1. Pour chaque colonne de valeur, définissez le type d’agrégation. 
    Vous pouvez définir une ou plusieurs colonnes de valeurs. Cette sélection s’apparente à l’utilisation de l’[opérateur summarize](kusto/query/summarizeoperator.md).

    **Regroupement de valeurs** <br> 
    Cliquez sur **+** à droite de **Group by (summarize)** pour sélectionner dans la liste déroulante une ou plusieurs colonnes qui seront utilisées pour réorganiser les valeurs en groupes. Cela équivaut à l’expression de groupe dans l’opérateur summarize.

1. Pour exécuter la requête, sélectionnez **Run query**.

    :::image type="content" source="media/grafana/query-builder-all-values.png" alt-text="Générateur de requêtes avec toutes les valeurs renseignées":::

    > [!TIP]
    > Lors de la finalisation des paramètres dans le générateur de requêtes, une requête en langage de requête Kusto est créée. Cette requête affiche la logique que vous avez construite avec l’éditeur de requête graphique. 

1. Sélectionnez **Edit KQL** pour basculer en mode brut et modifier votre requête en utilisant la flexibilité et la puissance du langage de requête Kusto.

:::image type="content" source="media/grafana/query-builder-with-raw-query.png" alt-text="Générateur de requêtes avec requête brute":::

### <a name="raw-mode"></a>Mode brut

Utilisez le mode RAW pour modifier votre requête. 

1. Dans le volet de requête, copiez la requête suivante, puis sélectionnez **Run Query**. La requête compartimente le nombre d’événements par jour pour l’exemple de jeu de données.

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

> [!IMPORTANT]
> Pour basculer en mode générateur de requêtes, sélectionnez **Switch to builder**. Grafana convertit la requête dans la logique disponible dans le générateur de requêtes. La logique du générateur de requêtes étant limitée, vous risquez de perdre des modifications manuelles apportées à la requête.

:::image type="content" source="media/grafana/raw-mode.png" alt-text="Basculer vers le générateur à partir du mode brut":::

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
