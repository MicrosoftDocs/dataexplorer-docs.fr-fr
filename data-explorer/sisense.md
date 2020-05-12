---
title: Visualiser des données Azure Data Explorer avec Sisense
description: Cet article explique comment configurer Azure Data Explorer en tant que source de données pour Sisense, ainsi que visualiser les données.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.custom: has-adal-ref
ms.date: 5/29/2019
ms.openlocfilehash: 4afbf9ddf53715b045dba46d03f233f1401ff770
ms.sourcegitcommit: f6cf88be736aa1e23ca046304a02dee204546b6e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82862154"
---
# <a name="visualize-data-from-azure-data-explorer-in-sisense"></a>Visualiser des données Azure Data Explorer dans Sisense

Sisense est une plateforme décisionnelle analytique qui vous permet de créer des applications analytiques qui offrent des expériences utilisateur hautement interactives. Le logiciel de génération de rapports décisionnels et de tableau de bord vous permet d’accéder à des données et de combiner celles-ci en quelques clics. Il vous permet de vous connecter à des sources de données structurées et non structurées, de joindre des tables de plusieurs sources avec un minimum de scripts et de codage, et de créer des tableaux de bord et des rapports web interactifs. Dans cet article, vous allez apprendre à configurer Azure Data Explorer en tant que source de données pour Sisense, ainsi qu’à visualiser les données d’un exemple de cluster.

## <a name="prerequisites"></a>Prérequis

Vous avez besoin des éléments suivants dans le cadre de cet article :

* [Télécharger et installer l’application Sisense](https://documentation.sisense.com/latest/getting-started/download-install.htm)

* Créer un cluster et une base de données qui inclut l’exemple de données StormEvents. Pour plus d’informations, consultez [Démarrage rapide : Créer un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md) et [Ingérer des exemples de données dans Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](includes/data-explorer-storm-events.md)]

## <a name="connect-to-sisense-dashboards-using-azure-data-explorer-jdbc-connector"></a>Se connecter à des tableaux de bord Sisense à l’aide du Connecteur JDBC d’Azure Data Explorer

1. Téléchargez et copiez les dernières versions des fichiers jar suivants vers *... \Sisense\DataConnectors\jdbcdrivers\adx*

    * activation-1.1.jar
    * adal4j-1.6.0.jar
    * commons-codec-1.10.jar
    * commons-collections4-4.1.jar
    * commons-lang3-3.5.jar
    * gson-2.8.0.jar
    * jcip-annotations-1.0-1.jar
    * json-smart-1.3.1.jar
    * lang-tag-1.4.4.jar
    * mail-1.4.7.jar
    * mssql-jdbc-7.2.1.jre8.jar
    * nimbus-jose-jwt-7.0.1.jar
    * oauth2-oidc-sdk-5.24.1.jar
    * slf4j-api-1.7.21.jar

1. Ouvrez l’application **Sisense**.
1. Sélectionnez l’onglet **Data** (Données), puis **+ ElastiCube** pour créer un modèle ElastiCube.

    ![Sélectionnez ElastiCube](media/sisense/data-select-elasticube.png)

1. Dans **Add new ElastiCube Model** (Ajouter un nouveau modèle ElastiCube), nommez le modèle ElastiCube, puis **enregistrez**.

    ![Ajouter un nouveau modèle ElastiCube](media/sisense/add-new-elasticube-model.png)

1. Sélectionnez **+ Data** (+ Données).

    ![Bouton de sélection de données](media/sisense/select-data.png)

1. Sous l’onglet **Select Connector**(Sélectionner un connecteur), sélectionnez le connecteur **Generic JDBC**.

    ![Choisir un connecteur JDBC](media/sisense/select-connector.png)

1. Sous l’onglet **Connect** (Connexion), renseignez les champs suivants pour le connecteur **JDBC générique**, puis sélectionnez **Suivant**.

    ![Paramètres du connecteur JDBC](media/sisense/jdbc-connector.png)

    |Champ |Description |
    |---------|---------|
    |Chaîne de connexion     |   `jdbc:sqlserver://<cluster_name.region>.kusto.windows.net:1433;database=<database_name>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.kusto.windows.net;loginTimeout=30;authentication=ActiveDirectoryPassword`      |
    |Dossier de fichiers JAR JDBC  |    `..\Sisense\DataConnectors\jdbcdrivers\adx`     |
    |Nom de classe du pilote    |   `com.microsoft.sqlserver.jdbc.SQLServerDriver`      |
    |User Name   |    Nom d’utilisateur AAD     |
    |Mot de passe     |   Nouveau mot de passe utilisateur AAD      |

1. Sous l’onglet **Select Data** (Sélectionner de données), recherchez **Select Database** (Sélectionner des données) pour sélectionner la base de données appropriée sur laquelle vous avez des autorisations. Dans cet exemple, sélectionnez *test1*.

    ![sélectionner la base de données](media/sisense/select-database.png)

1. Dans volet *test* (nom de la base de données) :
    1. Sélectionnez le nom de table pour afficher un aperçu de la table et des noms de ses colonnes. Vous pouvez supprimer les colonnes inutiles.
    1. Activez la case à cocher de la table appropriée pour la sélectionner.
    1. Sélectionnez **Terminé**.

    ![sélectionner une table](media/sisense/select-table-see-columns.png)

1. Sélectionnez **Build** pour générer votre jeu de données.

    * Dans la fenêtre **Build**, sélectionnez **Build** (Générer).

      ![Fenêtre Build](media/sisense/build-window.png)

    * Attendez que le processus de génération soit terminé, puis sélectionnez **Build Succeeded** (Génération réussie).

      ![Génération réussie](media/sisense/build-succeeded.png)

## <a name="create-sisense-dashboards"></a>Créer des tableaux de bord Sisense

1. Sous l’onglet **Analytics** (Analytique), sélectionnez **+**  > **New Dashboard** (Nouveau tableau de bord) pour créer des tableaux de bord sur ce jeu de données.

    ![Nouveau tableau de bord](media/sisense/new-dashboard.png)

1. Choisissez un tableau de bord, puis sélectionnez **Create** (Créer).

    ![Créer un tableau de bord](media/sisense/create-dashboard.png)

1. Sous **New Widget** (Nouveau widget), sélectionnez **+ Select Data** (+ Sélectionner des données) pour créer un widget.

    ![Ajouter des champs au tableau de bord StormEvents](media/sisense/storm-dashboard-add-field.png)

1. Sélectionnez **+ Add More Data** (+ Ajouter des données) pour ajouter des colonnes à votre graphique.

    ![Ajouter des données au graphique](media/sisense/add-more-data.png)

1. Sélectionnez **+ Widget** pour créer un autre widget. Glissez-déplacez les widgets pour réorganiser votre tableau de bord.

    ![Tableau de bord Storm](media/sisense/final-dashboard.png)

Vous pouvez désormais explorer vos données avec des analyses visuelles, créer des tableaux de bord supplémentaires et transformer des données en informations actionnables pour orienter votre activité.

## <a name="next-steps"></a>Étapes suivantes

* [Écrire des requêtes pour l’Explorateur de données Azure](write-queries.md)
