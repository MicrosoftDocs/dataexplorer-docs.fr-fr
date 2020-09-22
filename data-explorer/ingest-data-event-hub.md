---
title: Ingérer des données Event Hub dans Azure Data Explorer
description: Dans cet article, vous allez apprendre à ingérer (charger) des données dans Azure Data Explorer depuis Event Hub.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 69438457dfcbfc4e29805d5d193c227538910e45
ms.sourcegitcommit: 97404e9ed4a28cd497d2acbde07d00149836d026
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/21/2020
ms.locfileid: "90832640"
---
# <a name="ingest-data-from-event-hub-into-azure-data-explorer"></a>Ingérer des données Event Hub dans Azure Data Explorer

> [!div class="op_single_selector"]
> * [Portail](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Modèle Azure Resource Manager](data-connection-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

L’Explorateur de données Azure offre une ingestion (chargement de données) à partir d’Event Hubs, plateforme de streaming de big data et service d’ingestion d’événements. [Event Hubs](/azure/event-hubs/event-hubs-about) peut traiter des millions d’événements par seconde en quasi-temps réel. Dans cet article, vous créez un Event Hub, vous vous y connectez à partir d’Azure Data Explorer et vous voyez le flux de données via le système.

Pour obtenir des informations générales sur l’ingestion dans Azure Data Explorer à partir d’Event Hub, consultez [Connexion à Event Hub](ingest-data-event-hub-overview.md).

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* [Un cluster et une base de données de test](create-cluster-database-portal.md).
* [Un exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) qui génère des données et les envoie à un hub d’événements. Téléchargez l’exemple d’application sur votre système.
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pour exécuter l’exemple d’application.

## <a name="sign-in-to-the-azure-portal"></a>Connectez-vous au portail Azure.

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="create-an-event-hub"></a>Créer un hub d’événements

Dans cet article, vous générez des exemples de données et les envoyez à un Event Hub. La première étape consiste à créer un hub d’événements. Pour cela, utilisez un modèle Azure Resource Manager (ARM) dans le portail Azure.

1. Pour créer un hub d’événements, utilisez le bouton suivant pour démarrer le déploiement. Cliquez avec le bouton droit et sélectionnez **Ouvrir dans une nouvelle fenêtre** pour pouvoir suivre le reste des étapes de l’article.

    [![Bouton Déployer dans Azure](media/ingest-data-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

    Le bouton **Déployer sur Azure** vous amène dans le portail Azure pour remplir un formulaire de déploiement.

    ![Créer un formulaire Event Hub](media/ingest-data-event-hub/deploy-to-azure.png)

1. Sélectionnez l’abonnement dans lequel vous souhaitez créer le hub d’événements et créez un groupe de ressources nommé *test-hub-rg*.

    ![Créer un groupe de ressources](media/ingest-data-event-hub/create-resource-group.png)

1. Renseignez le formulaire avec les informations suivantes.

    ![Formulaire de déploiement](media/ingest-data-event-hub/deployment-form.png)

    Utilisez les valeurs par défaut pour tous les paramètres non listés dans le tableau suivant.

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Abonnement | Votre abonnement | Sélectionnez l’abonnement Azure que vous souhaitez utiliser pour votre hub d’événements.|
    | Resource group | *test-hub-rg* | Créez un groupe de ressources. |
    | Emplacement | *USA Ouest* | Pour cet article, sélectionnez *USA Ouest*. Pour un système de production, sélectionnez la région qui répond le mieux à vos besoins. Pour des performances optimales, créez l’espace de noms Event Hub au même emplacement que le cluster Kusto (en particulier pour les espaces de noms Event Hub ayant un débit élevé).
    | Nom de l’espace de noms | Nom unique de l’espace de noms | Choisissez un nom unique qui identifie votre espace de noms. Par exemple, *mytestnamespace*. Le nom de domaine *servicebus.windows.net* est ajouté au nom que vous fournissez. Le nom ne peut contenir que des lettres, des chiffres et des traits d’union. Le nom doit commencer par une lettre et se terminer par une lettre ou un chiffre. La valeur doit être comprise entre 6 et 50 caractères.
    | Nom du hub d’événements | *test-hub* | Le hub d’événements se trouve sous l’espace de noms, qui fournit un conteneur d’étendue unique. Le hub d’événements doit être unique dans l’espace de noms. |
    | Nom du groupe de consommateurs | *test-group* | Les groupes de consommateurs permettent que chacune des applications de consommation ait une vue distincte du flux d’événements. |
    | | |

1. Sélectionnez **Achat**, ce qui confirme que vous créez des ressources dans votre abonnement.

1. Dans la barre d’outils, sélectionnez **Notifications** pour superviser le processus de provisionnement. Le déploiement peut prendre plusieurs minutes, mais vous pouvez passer à l’étape suivante sans attendre.

    ![Icône Notifications](media/ingest-data-event-hub/notifications.png)

## <a name="create-a-target-table-in-azure-data-explorer"></a>Créer une table cible dans l’Explorateur de données Azure

Créez maintenant une table dans l’Explorateur de données Azure, à laquelle Event Hubs envoie des données. Créez la table dans le cluster et la base de données provisionnés dans **Prérequis**.

1. Dans le portail Azure, accédez à votre cluster, puis sélectionnez **Requête**.

    ![Lien d’application de requête](media/ingest-data-event-hub/query-explorer-link.png)

1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter** pour créer la table (TestTable) qui doit recevoir les données ingérées.

    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```

    ![Exécuter la requête de création](media/ingest-data-event-hub/run-create-query.png)

1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter** pour mapper les données JSON entrantes aux types de données et aux noms de colonne de la table (TestTable).

    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp", "Properties": {"Path": "$.timeStamp"}},{"column":"Name", "Properties": {"Path":"$.name"}} ,{"column":"Metric", "Properties": {"Path":"$.metric"}}, {"column":"Source", "Properties": {"Path":"$.source"}}]'
    ```

## <a name="connect-to-the-event-hub"></a>Se connecter au hub d’événements

Vous vous connectez maintenant au hub d’événements depuis l’Explorateur de données Azure. Lorsque cette connexion est en place, les données qui passent dans le hub d’événements sont diffusées vers la table de test que vous avez créée précédemment dans cet article.

1. Sélectionnez **Notifications** dans la barre d’outils pour vérifier que le déploiement du hub d’événements a réussi.

1. Sous le cluster que vous avez créé, sélectionnez **Bases de données** et **TestDatabase**.

    ![Sélectionner la base de données de test](media/ingest-data-event-hub/select-test-database.png)

1. Sélectionnez **Ingestion des données**, puis **Ajouter une connexion de données**. 

    :::image type="content" source="media/ingest-data-event-hub/event-hub-connection.png" alt-text="Sélectionner l’ingestion de données et ajouter une connexion de données dans le hub d’événements - Azure Data Explorer":::

### <a name="create-a-data-connection"></a>Créer une connexion de données

1. Renseignez le formulaire avec les informations suivantes :

    :::image type="content" source="media/ingest-data-event-hub/data-connection-pane.png" alt-text="Volet Connexion de données de hub d’événements - Azure Data Explorer":::

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la connexion de données | *test-hub-connection* | Nom de la connexion que vous souhaitez créer dans l’Explorateur de données Azure.|
    | Abonnement |      | ID d’abonnement dans lequel se trouve la ressource de hub d’événements.  |
    | Espace de noms Event Hub | Nom unique de l’espace de noms | Nom choisi précédemment qui identifie votre espace de noms. |
    | Event Hub | *test-hub* | Hub d’événements que vous avez créé. |
    | Groupe de consommateurs | *test-group* | Groupe de consommateurs défini dans le hub d’événements que vous avez créé. |
    | Propriétés du système d’événements | Sélectionner les propriétés pertinentes | [Propriétés système du hub d’événements](/azure/service-bus-messaging/service-bus-amqp-protocol-guide#message-annotations). S’il existe plusieurs enregistrements par message d’événement, les propriétés système sont ajoutées au premier. Lors de l’ajout des propriétés système, [créez](kusto/management/create-table-command.md) ou [mettez à jour](kusto/management/alter-table-command.md) le schéma de table et le [mappage](kusto/management/mappings.md) pour inclure les propriétés sélectionnées. |
    | Compression | *Aucun* | Type de compression de la charge utile des messages Event Hub. Types de compression pris en charge : *Aucun, GZip*.|
    
#### <a name="target-table"></a>Table cible

Deux options sont disponibles pour le routage des données ingérées : *statique* et *dynamique*. Dans le cadre de cet article, vous utilisez le routage statique, où vous spécifiez le nom de table, le format des données et le mappage comme valeurs par défaut. Si le message du hub d’événements comprend des informations de routage de données, ces informations de routage remplacent les paramètres par défaut.

1. Renseignez les paramètres de routage suivants :
  
   :::image type="content" source="media/ingest-data-event-hub/default-routing-settings.png" alt-text="Paramètres de routage par défaut pour l’ingestion des données dans le hub d’événements - Azure Data Explorer":::
        
   |**Paramètre** | **Valeur suggérée** | **Description du champ**
   |---|---|---|
   | Nom de la table | *TestTable* | Table que vous avez créée dans **TestDatabase**. |
   | Format de données | *JSON* | Les formats pris en charge sont Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO et W3CLOG. |
   | Mappage | *TestMapping* | [Mappage](kusto/management/mappings.md) que vous avez créé dans **TestDatabase**, qui mappe les données entrantes aux noms de colonnes et aux types de données de **TestTable**. Obligatoire pour les formats JSON, MULTILINE JSON et AVRO, et facultatif pour les autres formats.|
    
   > [!NOTE]
   > * Vous n’êtes pas obligé de spécifier tous les **paramètres de routage par défaut**. Des paramètres partiels sont également acceptés.
   > * Seuls les événements mis en file d’attente après que vous avez créé la connexion de données sont ingérés.

1. Sélectionnez **Create** (Créer). 

### <a name="event-system-properties-mapping"></a>Mappage des propriétés du système d’événements

> [!Note]
> * Les propriétés système sont prises en charge pour les événements à enregistrement unique.
> * Pour un mappage `csv`, des propriétés sont ajoutées au début de l’enregistrement. Pour un mappage `json`, des propriétés sont ajoutées en fonction du nom qui s’affiche dans la liste déroulante.

Si vous avez sélectionné **Propriétés du système d’événements** dans la section **Source de données** de la table, vous devez inclure des [propriétés système](ingest-data-event-hub-overview.md#system-properties) dans le schéma et le mappage de table.


## <a name="copy-the-connection-string"></a>Copier la chaîne de connexion

Lorsque vous exécutez l’[application exemple](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) mentionnée dans les composants requis, vous avez besoin de la chaîne de connexion pour l’espace de noms du hub d’événements.

1. Sous l’espace de noms du hub d’événements que vous avez créé, sélectionnez **Stratégies d’accès partagé**, puis **RootManageSharedAccessKey**.

    ![Stratégies d’accès partagé](media/ingest-data-event-hub/shared-access-policies.png)

1. Copiez **Chaîne de connexion - Clé primaire**. Collez-la dans la prochaine section.

    ![Chaîne de connexion](media/ingest-data-event-hub/connection-string.png)

## <a name="generate-sample-data"></a>Générer un exemple de données

Utilisez l’[exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que vous avez téléchargé pour générer les données.

1. Ouvrez la solution de l’application exemple dans Visual Studio.

1. Dans le fichier *program.cs*, mettez à jour la constante `eventHubName` avec le nom de votre hub d’événements et mettez à jour la constante `connectionString` avec la chaîne de connexion que vous avez copiée à partir de l’espace de noms Event Hub.

    ```csharp
    const string eventHubName = "test-hub";
    // Copy the connection string ("Connection string-primary key") from your Event Hub namespace.
    const string connectionString = @"<YourConnectionString>";
    ```

1. Générez et exécutez l'application. L’application envoie des messages au hub d’événements et affiche un état toutes les dix secondes.

1. Une fois que l’application a envoyé quelques messages, passez à l’étape suivante : examiner le flux de données dans votre hub d’événements et votre table de test.

## <a name="review-the-data-flow"></a>Examiner le flux de données

Avec l’application générant des données, vous pouvez maintenant voir le flux de données depuis le hub d’événements vers la table dans votre cluster.

1. Dans le portail Azure, sous votre hub d’événements, vous voyez le pic de l’activité pendant l’exécution de l’application.

    ![Graphe du hub d’événements](media/ingest-data-event-hub/event-hub-graph.png)

1. Exécutez la requête suivante dans votre base de données de test pour vérifier combien de messages sont arrivés dans la base de données jusqu’à présent.

    ```Kusto
    TestTable
    | count
    ```

1. Exécutez la requête suivante pour voir le contenu des messages :

    ```Kusto
    TestTable
    ```

    Le jeu de résultats doit ressembler à ce qui suit :

    ![Jeu de résultats des messages](media/ingest-data-event-hub/message-result-set.png)

    > [!NOTE]
    > * Azure Data Explorer est associé à une stratégie d’agrégation (traitement par lot) conçue pour optimiser le processus d’ingestion des données. La stratégie est configurée sur 5 minutes ou 500 Mo de données, ce qui peut entraîner une certaine latence. Consultez la [stratégie de traitement par lot](kusto/management/batchingpolicy.md) pour les options d’agrégation. 
    > * L’ingestion de Event Hub comprend le temps de réponse de Event Hub de 10 secondes ou 1 Mo. 
    > * Configurez votre tableau pour prendre en charge la diffusion en continu et supprimez le décalage dans le temps de réponse. Consultez la [stratégie de diffusion en continu](kusto/management/streamingingestionpolicy.md). 

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de réutiliser votre hub d’événements, nettoyez **test-hub-rg** pour éviter des frais.

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche, puis sélectionnez le groupe de ressources que vous avez créé.  

    Si le menu de gauche est réduit, sélectionnez le ![Bouton Développer](media/ingest-data-event-hub/expand.png) pour le développer.

   ![Sélectionner un groupe de ressources à supprimer](media/ingest-data-event-hub/delete-resources-select.png)

1. Sous **test-resource-group**, sélectionnez **Supprimer le groupe de ressources**.

1. Dans la nouvelle fenêtre, tapez le nom du groupe de ressources à supprimer (*test-hub-rg*), puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans Azure Data Explorer](web-query-data.md)
