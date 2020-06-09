---
title: 'Tutoriel : Visualiser des données Azure Data Explorer dans Power BI'
description: Dans ce tutoriel, vous découvrez comment vous connecter à l’Explorateur de données Azure avec Power BI et comment visualiser vos données.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: tutorial
ms.date: 06/02/2020
ms.openlocfilehash: bde567a3c41a226ca0a8304d7ab4ed42f3fa65bf
ms.sourcegitcommit: 8953d09101f4358355df60ab09e55e71bc255ead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84420915"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Tutoriel : Visualiser des données Azure Data Explorer dans Power BI

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Power BI est une solution d’analytique métier qui vous permet de visualiser vos données et de partager les résultats dans votre organisation. Dans ce tutoriel, vous découvrez d’abord comment afficher des visuels dans l’Explorateur de données Azure. Vous vous connectez ensuite à l’Explorateur de données Azure avec Power BI, vous générez un rapport basé sur des exemples de données et vous le publiez sur le service Power BI.

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer. Si vous n’êtes pas inscrit pour Power BI Pro, [inscrivez-vous pour un essai gratuit](https://app.powerbi.com/signupredirect?pbi_source=web) avant de commencer.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Afficher des visuels dans l’Explorateur de données Azure
> * Se connecter à l’Explorateur de données Azure dans Power BI Desktop
> * Utiliser des données dans Power BI Desktop
> * Créer un rapport avec des visuels
> * Publier et partager le rapport

## <a name="prerequisites"></a>Prérequis

En plus des abonnements Azure et Power BI, les éléments suivants sont nécessaires pour effectuer ce tutoriel :

* [Un cluster et une base de données de test](create-cluster-database-portal.md)

* [Les exemples de données StormEvents](ingest-sample-data.md) [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (sélectionnez **TÉLÉCHARGER GRATUITEMENT**)

## <a name="render-visuals-in-azure-data-explorer"></a>Afficher des visuels dans l’Explorateur de données Azure

Avant de passer à Power BI, voyons comment afficher des visuels dans l’Explorateur de données Azure. C’est pratique pour certaines analyses rapides.

1. Connectez-vous à [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Dans le volet gauche, sélectionnez la base de données de test qui contient les exemples de données StormEvents.

1. Collez la requête suivante dans la fenêtre de droite, puis sélectionnez **Exécuter**.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Cette requête compte les événements météorologiques par état. Elle affiche ensuite un histogramme pour tous les états qui ont plus de 1 800 événements météorologiques.

    ![Histogramme des événements](media/visualize-power-bi/events-column-chart.png)

1. Collez la requête suivante dans la fenêtre de droite, puis sélectionnez **Exécuter**.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Cette requête compte les événements météorologiques par type pour le mois de juillet dans l’état de Washington. Elle affiche ensuite un graphique à secteurs montrant le pourcentage de chaque type d’événement.

    ![Graphique à secteurs des événements](media/visualize-power-bi/events-pie-chart.png)

Il est maintenant temps de regarder Power BI, mais il est bien sûr possible d’en faire beaucoup plus avec les visuels dans l’Explorateur de données Azure.

## <a name="connect-to-azure-data-explorer"></a>Se connecter à l’Explorateur de données Azure

Vous vous connectez maintenant à l’Explorateur de données Azure dans Power BI Desktop.

1. Dans Power BI Desktop, sous l’onglet **Accueil**, sélectionnez **Obtenir les données**, puis **Plus**.

    ![Obtenir des données](media/visualize-power-bi/get-data-more.png)

1. Recherchez *Azure Data Explorer*, sélectionnez **Azure Data Explorer**, puis **Se connecter**.

    ![Rechercher et obtenir des données](media/visualize-power-bi/search-get-data.png)

1. Dans l’écran **Connecteur en préversion**, sélectionnez **Continuer**.

1. Dans l’écran suivant, entrez vos noms de cluster et de base de données de test. Le cluster doit être de la forme `https://<ClusterName>.<Region>.kusto.windows.net`. Entrez *StormEvents* pour le nom de la table. Laissez toutes les autres options avec les valeurs par défaut, puis sélectionnez **OK**.

    ![Options pour le cluster, la base de données et la table](media/visualize-power-bi/cluster-database-table.png)

1. Dans l’écran d’aperçu des données, sélectionnez **Modifier**.

    La table s’ouvre dans l’éditeur Power Query, où vous pouvez modifier les lignes et les colonnes avant d’importer les données.

## <a name="work-with-data-in-power-bi-desktop"></a>Utiliser des données dans Power BI Desktop

Maintenant que vous avez une connexion à l’Explorateur de données Azure, vous modifiez les données dans l’éditeur Power Query. Vous supprimez les lignes avec des valeurs null dans la colonne **BeginLat** et vous supprimez entièrement la colonne JSON **StormSummary**. Il s’agit d’opérations simples, mais vous pouvez également effectuer des transformations complexes lors de l’importation de données.

1. Sélectionnez la flèche pour la colonne **BeginLat**, décochez la case **Null**, puis sélectionnez **OK**.

    ![Filtrer une colonne](media/visualize-power-bi/filter-column.png)

1. Cliquez sur l’en-tête de colonne **StormSummary**, puis sélectionnez **Supprimer**.

    ![Supprimer une colonne](media/visualize-power-bi/remove-column.png)

1. Dans le volet **PARAMÈTRES D’UNE REQUÊTE**, changez le nom *Query1* en *StormEvents*.

    ![Changer le nom d’une requête](media/visualize-power-bi/query-name.png)

1. Sous l’onglet **Accueil** du ruban, sélectionnez **Fermer et appliquer**.

    ![Fermer et appliquer](media/visualize-power-bi/close-apply.png)

    Power Query applique vos modifications, puis importe les exemples de données dans un *modèle de données*. Les étapes suivantes montrent comment enrichir ce modèle. Là encore, il ne s’agit que d’un exemple simple pour donner une idée de ce qui est possible.

1. Sur le côté gauche de la fenêtre principale, sélectionnez la vue de données.

    ![Vue de données](media/visualize-power-bi/data-view.png)

1. Sous l’onglet **Modélisation** du ruban, sélectionnez **Nouvelle colonne**.

    ![Nouvelle colonne](media/visualize-power-bi/new-column.png)

1. Entrez la formule DAX (Data Analysis Expressions) suivante dans la barre de formule, puis appuyez sur Entrée.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Barre de formule](media/visualize-power-bi/formula-bar.png)

    Cette formule crée la colonne *DurationHours*, qui calcule la durée en heures de chaque événement météorologique. Vous utilisez cette colonne dans un visuel dans la section suivante.

1. Faites défiler la table vers la droite pour voir la colonne.

## <a name="create-a-report-with-visuals"></a>Créer un rapport avec des visuels

Maintenant que les données sont importées et que vous avez amélioré le modèle de données, il est temps de générer un rapport avec des visuels. Vous ajoutez un histogramme basé sur la durée des événements et une carte qui montre les dégâts dans les cultures.

1. Sur le côté gauche de la fenêtre, sélectionnez la vue Rapport.

    ![Vue Rapport](media/visualize-power-bi/report-view.png)

1. Dans le volet **VISUALISATIONS**, sélectionnez l’histogramme groupé.

    ![Ajouter un histogramme](media/visualize-power-bi/add-column-chart.png)

    Un graphique vide est ajouté au canevas.

    ![Graphique vide](media/visualize-power-bi/blank-chart.png)

1. Dans la liste **CHAMPS**, sélectionnez **DurationHours** (Durée en heures) et **State** (État).

    ![Sélectionner des champs](media/visualize-power-bi/select-fields.png)

    Vous disposez maintenant d’un graphique qui montre le nombre total d’heures des événements météorologiques par état au cours d’une année.

    ![Histogramme Durée](media/visualize-power-bi/duration-column-chart.png)

1. Cliquez n’importe où sur le canevas en dehors de l’histogramme.

1. Dans le volet **VISUALISATIONS**, sélectionnez la carte.

    ![Ajouter une carte](media/visualize-power-bi/add-map.png)

1. Dans la liste **CHAMPS**, sélectionnez **CropDamage** (Dégâts aux cultures) et **State** (État). Redimensionnez la carte de façon à voir clairement les états des États-Unis.

    ![Carte des dégâts aux cultures](media/visualize-power-bi/crop-damage-map.png)

    La taille des bulles représente la valeur en dollars des dégâts aux cultures. Placez la souris sur les bulles pour voir les détails.

1. Déplacez et redimensionnez les visuels de façon à obtenir un rapport qui ressemble à l’image suivante.

    ![Rapport terminé](media/visualize-power-bi/finished-report.png)

1. Enregistrez le rapport sous le nom *storm-events.pbix*.

## <a name="publish-and-share-the-report"></a>Publier et partager le rapport

À ce stade, le travail que vous avez effectué dans Power BI a été entièrement local, avec Power BI Desktop. Maintenant, vous publiez le rapport sur le service Power BI, où vous pouvez le partager avec d’autres utilisateurs.

1. Dans Power BI Desktop, sous l’onglet **Accueil** du ruban, sélectionnez **Publier**.

    ![Bouton Publier](media/visualize-power-bi/publish-button.png)

1. Si vous n’êtes pas déjà connecté à Power BI, effectuez le processus de connexion.

1. Sélectionnez **Mon espace de travail**, puis **Sélectionner**.

    ![Sélectionner un espace de travail](media/visualize-power-bi/select-workspace.png)

1. Une fois la publication terminée, sélectionnez **Ouvrir storm-events.pbix dans Power BI**.

    ![Publication réussie](media/visualize-power-bi/publishing-succeeded.png)

    Le rapport s’ouvre dans le service, avec les mêmes visuels et la même disposition que ce que vous avez défini dans Power BI Desktop.

1. Dans le coin supérieur droit du rapport, sélectionnez **Partager**.

    ![Bouton Partager](media/visualize-power-bi/share-button.png)

1. Dans l’écran **Partager le rapport**, ajoutez un collègue de votre organisation, ajoutez une note, puis sélectionnez **Partager**.

    ![Partager un rapport](media/visualize-power-bi/share-report.png)

    Si votre collègue dispose des autorisations appropriées, il peut accéder au rapport que vous avez partagé.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne souhaitez pas conserver le rapport que vous avez créé, supprimez simplement le fichier *storm-events.pbix*. Si vous voulez supprimer le rapport que vous avez publié, effectuez les étapes suivantes.

1. Sous **Mon espace de travail**, faites défiler jusqu’à **RAPPORTS** et recherchez **storm-events**.

1. Sélectionnez les points de suspension ( **...** ) en regard de **storm-events**, puis sélectionnez **SUPPRIMER**.

    ![Supprimer un rapport](media/visualize-power-bi/remove-report.png)

1. Confirmez la suppression.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Écrire des requêtes](write-queries.md)
