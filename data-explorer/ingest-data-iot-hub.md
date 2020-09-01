---
title: Ingérer les données d’un hub IoT dans Azure Data Explorer
description: Dans cet article, vous allez apprendre à ingérer (charger) des données dans Azure Data Explorer depuis un hub IoT.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 01/08/2020
ms.openlocfilehash: 578141c63daeecd285d397b356260a4f22720621
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874934"
---
# <a name="ingest-data-from-iot-hub-into-azure-data-explorer"></a>Ingérer les données d’un hub IoT dans Azure Data Explorer 

> [!div class="op_single_selector"]
> * [Portail](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Modèle Azure Resource Manager](data-connection-iot-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

Cet article vous montre comment ingérer des données dans Azure Data Explorer depuis IoT Hub, plateforme de streaming de big data et service d’ingestion IoT.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Créez [un cluster de test et une base de données](create-cluster-database-portal.md) avec le nom de base de données *testdb*.
* [Exemple d’application](https://github.com/Azure-Samples/azure-iot-samples-csharp) et de documentation pour simuler un appareil.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pour exécuter l’exemple d’application.

## <a name="create-an-iot-hub"></a>Créer un hub IoT

[!INCLUDE [iot-hub-include-create-hub](includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device-to-the-iot-hub"></a>Inscrire un appareil au hub IoT

[!INCLUDE [iot-hub-get-started-create-device-identity](includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-target-table-in-azure-data-explorer"></a>Créer une table cible dans l’Explorateur de données Azure

Créez maintenant une table dans Azure Data Explorer, à laquelle les hubs IoT enverront des données. Créez la table dans le cluster et la base de données provisionnés dans [**Prérequis**](#prerequisites).

1. Dans le portail Azure, accédez à votre cluster, puis sélectionnez **Requête**.

    ![Requête ADX dans le portail](media/ingest-data-iot-hub/adx-initiate-query.png)

1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter** pour créer la table (TestTable) qui doit recevoir les données ingérées.

    ```Kusto
    .create table TestTable (temperature: real, humidity: real)
    ```
    
    ![Exécuter la requête de création](media/ingest-data-iot-hub/run-create-query.png)

1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter** pour mapper les données JSON entrantes aux types de données et aux noms de colonne de la table (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"humidity","path":"$.humidity","datatype":"real"},{"column":"temperature","path":"$.temperature","datatype":"real"}]'
    ```

## <a name="connect-azure-data-explorer-table-to-iot-hub"></a>Connecter la table Azure Data Explorer au hub IoT

Connectez-vous maintenant au hub IoT depuis Azure Data Explorer. Une fois cette connexion effectuée, les données qui circulent dans le hub IoT sont diffusées dans la [table cible que vous avez créée](#create-a-target-table-in-azure-data-explorer).

1. Sélectionnez **Notifications** dans la barre d’outils pour vérifier que le déploiement du hub IoT a réussi.

1. Sous le cluster que vous avez créé, sélectionnez **Bases de données**, puis sélectionnez la base de données que vous avez créée, **testdb**.
    
    ![Sélectionner la base de données de test](media/ingest-data-iot-hub/select-database.png)

1. Sélectionnez **Ingestion des données**, puis **Ajouter une connexion de données**. Renseignez ensuite le formulaire avec les informations ci-après. Sélectionnez **Créer** après avoir terminé.

    ![Connexion au hub IoT](media/ingest-data-iot-hub/iot-hub-connection.png)

    **Source de données** :

    **Paramètre** | **Description du champ**
    |---|---|
    | Nom de la connexion de données | Nom de la connexion que vous souhaitez créer dans Azure Data Explorer
    | IoT Hub | Nom de l’IoT Hub |
    | Stratégie d’accès partagé | Nom de la stratégie d’accès partagé. Doit avoir des autorisations de lecture |
    | Groupe de consommateurs |  Groupe de consommateurs défini dans le point de terminaison intégré au hub IoT |
    | Propriétés du système d’événements | [Propriétés système d’événement du hub IoT](/azure/iot-hub/iot-hub-devguide-messages-construct#system-properties-of-d2c-iot-hub-messages). Lors de l’ajout des propriétés système, [créez](kusto/management/create-table-command.md) ou [mettez à jour](kusto/management/alter-table-command.md) le schéma de table et le [mappage](kusto/management/mappings.md) pour inclure les propriétés sélectionnées. | | | 

    > [!NOTE]
    > En cas de [basculement manuel](/azure/iot-hub/iot-hub-ha-dr#manual-failover), vous devez recréer la connexion de données.

    **Table cible** :

    Deux options sont disponibles pour le routage des données ingérées : *statique* et *dynamique*. 
    Dans le cadre de cet article, vous utilisez le routage statique, où vous spécifiez le nom de table, le format des données et le mappage. Par conséquent, ne sélectionnez pas **My data includes routing info** (Mes données incluent des informations de routage).

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Table de charge de travail | *TestTable* | Table que vous avez créée dans **testdb**. |
    | Format de données | *JSON* | Les formats pris en charge sont Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO et W3CLOG.|
    | Mappage de colonnes | *TestMapping* | [Mappage](kusto/management/mappings.md) que vous avez créé dans **testdb**, qui mappe les données JSON entrantes aux noms de colonnes et aux types de données de **testdb**. Obligatoire pour les formats JSON, MULTILINE JSON et AVRO, et facultatif pour les autres formats.|
    | | |

    > [!NOTE]
    > * Sélectionnez **My data includes routing info** pour utiliser le routage dynamique, où vos données incluent les informations de routage nécessaires comme indiqué dans les commentaires de l’[exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest). Si à la fois des propriétés statiques et des propriétés dynamiques sont définies, les propriétés dynamiques remplacent les propriétés statiques. 
    > * Seuls les événements mis en file d’attente après que vous avez créé la connexion de données sont ingérés.

[!INCLUDE [data-explorer-container-system-properties](includes/data-explorer-container-system-properties.md)]

## <a name="generate-sample-data-for-testing"></a>Générer des exemples de données pour les tests

L’application d’appareil simulé se connecte à un point de terminaison spécifique de l’appareil sur votre IoT Hub et envoie les données de télémétrie simulée (température et humidité).

1. Téléchargez l’exemple de projet C# à partir de https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip et extrayez l’archive ZIP.

1. Dans une fenêtre de terminal local, accédez au dossier racine de l’exemple de projet C#. Ensuite, accédez au dossier **iot-hub\Quickstarts\simulated-device**.

1. Utilisez un éditeur de texte pour ouvrir le fichier **SimulatedDevice.cs**.

    Remplacez la valeur de la variable `s_connectionString` par la chaîne de connexion d’appareil obtenur dans [Inscrire un appareil au hub IoT](#register-a-device-to-the-iot-hub). Puis, enregistrez les modifications apportées au fichier **SimulatedDevice.cs**.

1. Dans la fenêtre de terminal local, exécutez les commandes suivantes pour installer les packages requis pour l’application d’appareil simulé :

    ```cmd/sh
    dotnet restore
    ```

1. Dans la fenêtre de terminal local, exécutez la commande suivante pour générer et exécuter l’application d’appareil simulé :

    ```cmd/sh
    dotnet run
    ```

    La capture d’écran suivante présente la sortie lorsque l’application d’appareil simulé envoie des données de télémétrie à votre IoT Hub :

    ![Exécuter l’appareil simulé](media/ingest-data-iot-hub/simulated-device.png)

## <a name="review-the-data-flow"></a>Examiner le flux de données

L’application générant des données, vous pouvez maintenant voir le flux de données depuis le hub IoT vers la table figurant dans votre cluster.

1. Dans le portail Azure, sous votre hub IoT, vous voyez le pic de l’activité pendant l’exécution de l’application.

    ![Métriques IoT Hub](media/ingest-data-iot-hub/iot-hub-metrics.png)

1. Exécutez la requête suivante dans votre base de données de test pour vérifier combien de messages sont arrivés dans la base de données jusqu’à présent.

    ```Kusto
    TestTable
    | count
    ```

1. Exécutez la requête suivante pour voir le contenu des messages :

    ```Kusto
    TestTable
    ```

    Jeu de résultats :
    
    ![Afficher les résultats des données ingérées](media/ingest-data-iot-hub/show-ingested-data.png)

    > [!NOTE]
    > * Azure Data Explorer est associé à une stratégie d’agrégation (traitement par lot) conçue pour optimiser le processus d’ingestion des données. La stratégie est configurée sur 5 minutes ou 500 Mo de données, ce qui peut entraîner une certaine latence. Consultez la [stratégie de traitement par lot](kusto/management/batchingpolicy.md) pour les options d’agrégation. 
    > * Configurez votre tableau pour prendre en charge la diffusion en continu et supprimez le décalage dans le temps de réponse. Consultez la [stratégie de diffusion en continu](kusto/management/streamingingestionpolicy.md). 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de réutiliser votre hub IoT, nettoyez votre groupe de ressources pour éviter des frais.

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche, puis sélectionnez le groupe de ressources que vous avez créé.  

    Si le menu de gauche est réduit, sélectionnez le ![Bouton Développer](media/ingest-data-event-hub/expand.png) pour le développer.

   ![Sélectionner un groupe de ressources à supprimer](media/ingest-data-iot-hub/delete-resources-select.png)

1. Sous **test-resource-group**, sélectionnez **Supprimer le groupe de ressources**.

2. Dans la nouvelle fenêtre, tapez le nom du groupe de ressources à supprimer, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans Azure Data Explorer](web-query-data.md)
