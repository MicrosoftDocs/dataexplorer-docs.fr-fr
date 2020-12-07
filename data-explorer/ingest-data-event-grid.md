---
title: Ingérer des objets blob Azure dans Azure Data Explorer
description: Cet article vous montre comment envoyer des données de compte de stockage à Azure Data Explorer en utilisant un abonnement Event Grid.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 2c5c5cbb15e55b585bae632a960909070c724eb8
ms.sourcegitcommit: 4d5628b52b84f7564ea893f621bdf1a45113c137
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444218"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingérer des objets blob dans Azure Data Explorer en s’abonnant à des notifications Event Grid

> [!div class="op_single_selector"]
> * [Portail](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]

Cet article vous montre comment ingérer des objets blob de votre compte de stockage vers Azure Data Explorer en utilisant une connexion de données Event Grid. Vous allez créer une connexion de données Event Grid qui définit un abonnement [Azure Event Grid](/azure/event-grid/overview). L’abonnement Event Grid achemine les événements de votre compte de stockage vers Azure Data Explorer via un hub d’événements Azure. Ensuite, vous verrez un exemple de flux de données dans tout le système. 

Pour obtenir des informations générales sur l’ingestion dans Azure Data Explorer à partir d’Event Grid, consultez [Se connecter à Event Grid](ingest-data-event-grid-overview.md). Pour créer manuellement des ressources dans le portail Azure, consultez [Créer manuellement des ressources pour l’ingestion Event Grid](ingest-data-event-grid-manual.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Créez un [compte Azure gratuit](https://azure.microsoft.com/free/).
* [Un cluster et une base de données](create-cluster-database-portal.md).
* [Un compte de stockage](/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
    * L’abonnement aux notifications Event Grid peut être défini sur des comptes de stockage Azure pour `BlobStorage`, `StorageV2` ou [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction).


## <a name="create-a-target-table-in-azure-data-explorer"></a>Créer une table cible dans l’Explorateur de données Azure

Créez une table dans Azure Data Explorer, à laquelle Event Hubs enverra les données. Créez la table dans le cluster et la base de données préparés dans en lien avec les conditions préalables.

1. Dans le portail Azure, sous votre cluster, sélectionnez **Requête**.

    :::image type="content" source="media/ingest-data-event-grid/query-explorer-link.png" alt-text="Lien vers l’explorateur de requêtes"::: 

1. Copiez la commande suivante dans la fenêtre, puis sélectionnez **Exécuter** pour créer la table (TestTable) qui doit recevoir les données ingérées.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    :::image type="content" source="media/ingest-data-event-grid/run-create-table.png" alt-text="Exécuter la commande pour créer la table":::

1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter** pour mapper les données JSON entrantes aux types de données et aux noms de colonne de la table (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Créer une connexion de données Event Grid dans Azure Data Explorer

Connectez maintenant le compte de stockage à Azure Data Explorer afin que le flux de données dans le stockage soit envoyé en streaming à la table de test. 

1. Sous le cluster que vous avez créé, sélectionnez **Bases de données** > **TestDatabase**.

    :::image type="content" source="media/ingest-data-event-grid/select-test-database.png" alt-text="Sélectionner la base de données de test":::

1. Sélectionnez **Ingestion des données** > **Ajouter la connexion de données**.

    :::image type="content" source="media/ingest-data-event-grid/data-ingestion-create.png" alt-text="Ajouter une connexion de données pour l’ingestion des données":::

### <a name="data-connection---basics-tab"></a>Connexion de données - Onglet Informations de base

1. Sélectionnez le type de connexion : **Stockage Blob**.

1. Renseignez le formulaire avec les informations suivantes :

    :::image type="content" source="media/ingest-data-event-grid/data-connection-basics.png" alt-text="Remplir le formulaire Event Grid avec les informations de base de la connexion":::

    |**Paramètre** | **Valeur suggérée** | **Description du champ**|
    |---|---|---|
    | Nom de la connexion de données | *test-grid-connection* | Nom de la connexion que vous souhaitez créer dans Azure Data Explorer.|
    | Abonnement du compte de stockage | Votre ID d’abonnement | ID d’abonnement où se trouve votre compte de stockage.|
    | Compte de stockage | *gridteststorage1* | Nom du compte de stockage que vous avez créé précédemment.|
    | Type d'événement | *Objet blob créé* ou *Objet blob renommé* | Type d’événement qui déclenche l’ingestion. |
    | Création de ressources | *Automatique* | Spécifiez si vous voulez qu’Azure Data Explorer crée un abonnement Event Grid, un espace de noms Event Hub et un hub d’événements pour vous. Pour créer manuellement des ressources, consultez [Créer manuellement des ressources pour l’ingestion Event Grid](ingest-data-event-grid-manual.md).|

1. Sélectionnez **Paramètres de filtre** si vous voulez suivre des sujets spécifiques. Définissez les filtres pour les notifications comme suit :
    * Le champ **Préfixe** est le préfixe *littéral* du sujet. Comme le modèle appliqué est *startswith*, il peut englober plusieurs conteneurs, dossiers ou objets blob. Les caractères génériques ne sont pas autorisés.
        * Pour définir un filtre sur le conteneur d’objets blob, le champ *doit* être défini comme suit : *`/blobServices/default/containers/[container prefix]`* .
        * Pour définir un filtre sur un préfixe d’objet blob (ou un dossier dans Azure Data Lake Gen2), le champ *doit* être défini comme suit : *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* .
    * Le champ **Suffixe** est le suffixe *littéral* de l’objet blob. Les caractères génériques ne sont pas autorisés.
    * Le champ **Sensible à la casse** indique si les filtres de préfixe et de suffixe respectent la casse.
    * Pour plus d’informations sur le filtrage des événements, consultez [Événements du stockage Blob](/azure/storage/blobs/storage-blob-event-overview#filtering-events).
    
    :::image type="content" source="media/ingest-data-event-grid/filter-settings.png" alt-text="Paramètres de filtre Event Grid":::    

1. Sélectionnez **Suivant : Propriétés d’ingestion**.

### <a name="data-connection---ingest-properties-tab"></a>Connexion de données - Onglet Propriétés d’ingestion

1. Renseignez le formulaire avec les informations suivantes. Les noms de table et de mappage sont sensibles à la casse :

   :::image type="content" source="media/ingest-data-event-grid/data-connection-ingest-properties.png" alt-text="Vérifier et créer les propriétés d’ingestion de table et de mappage":::

    Propriétés d’ingestion :

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la table | *TestTable* | Table que vous avez créée dans **TestDatabase**. |
    | Format de données | *JSON* | Les formats pris en charge sont Avro, CSV, JSON, MULTILINE JSON, ORC, PARQUET, PSV, SCSV, SOHSV, TSV, TXT, TSVE, APACHEAVRO, RAW et W3CLOG. Les options de compression prises en charge sont Zip et GZip. |
    | Mappage | *TestMapping* | Le mappage que vous avez créé dans **TestDatabase**, qui mappe les données JSON entrantes dans les colonnes des noms de colonne et les types de données de **TestTable**.|
    | Paramètres avancés | *Mes données comprennent des en-têtes* | Ignore les en-têtes. Pris en charge pour les fichiers de type *SV.|

   > [!NOTE]
   > Vous n’êtes pas obligé de spécifier tous les **paramètres de routage par défaut**. Des paramètres partiels sont également acceptés.
1. Sélectionnez **Suivant : Vérifier + créer**

### <a name="data-connection---review--create-tab"></a>Connexion de données - Onglet Vérifier + créer

1. Passez en revue les ressources qui ont été créées automatiquement pour vous et sélectionnez **Créer**.

    :::image type="content" source="media/ingest-data-event-grid/create-event-grid-data-connection-review-create.png" alt-text="Vérifier et créer la connexion de données pour Event Grid":::

### <a name="deployment"></a>Déploiement

Attendez la fin du déploiement. Si votre déploiement a échoué, sélectionnez **Détails de l’opération** à côté de la phase qui a échoué pour obtenir plus d’informations sur la raison de l’échec. Sélectionnez **Redéployer** pour retenter de déployer les ressources. Vous pouvez modifier les paramètres avant le déploiement.

:::image type="content" source="media/ingest-data-event-grid/deploy-event-grid-resources.png" alt-text="Déployer les ressources Event Grid":::

## <a name="generate-sample-data"></a>Générer un exemple de données

Maintenant qu’Azure Data Explorer et le compte de stockage sont connectés, vous pouvez créer des exemples de données et les charger dans le conteneur de stockage.

Nous allons maintenant utiliser un petit script de shell qui exécute quelques commandes Azure CLI de base pour interagir avec les ressources de Stockage Azure. Ce script effectue les actions suivantes : 
1. Crée un nouveau conteneur dans votre compte de stockage.
1. Charge un fichier existant (comme un objet blob) sur ce conteneur.
1. Liste les objets blob du conteneur. 

Vous pouvez utiliser [Azure Cloud Shell](/azure/cloud-shell/overview) pour exécuter le script directement dans le portail.

Enregistrez les données dans un fichier et chargez celui-ci avec ce script :

```json
{"TimeStamp": "1987-11-16 12:00","Value": "Hello World","Source": "TestSource"}
```

```azurecli
#!/bin/bash
### A simple Azure Storage example script

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export file_to_upload=<file_to_upload>
    export destination_file=<destination_file>

    echo "Creating the container..."
    az storage container create --name $container_name

    echo "Uploading the file..."
    az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name --metadata "rawSizeBytes=1024"

    echo "Listing the blobs..."
    az storage blob list --container-name $container_name --output table

    echo "Done"
```

> [!NOTE]
> Pour obtenir les meilleures performances d’ingestion, la taille *non compressée* des objets blob compressés soumis pour l’ingestion doit être communiquée. Étant donné que les notifications Event Grid contiennent uniquement des détails de base, les informations de taille doivent être communiquées explicitement. Les informations de taille non compressée peuvent être fournies en définissant la propriété `rawSizeBytes` sur les métadonnées d’objet blob avec la taille de données *non compressée* en octets.

### <a name="ingestion-properties"></a>Propriétés d’ingestion

Vous pouvez spécifier les [propriétés d’ingestion](ingest-data-event-grid-overview.md#ingestion-properties) de l’ingestion d’objets blob via les métadonnées d’objet blob. 

> [!NOTE]
> Azure Data Explorer ne supprimera pas les objets blob après l’ingestion.
> Conservez les objets blob pendant trois à cinq jours.
> Pour gérer la suppression des objets blob, utilisez le [cycle de vie du stockage Blob Azure](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal). 

## <a name="review-the-data-flow"></a>Examiner le flux de données

> [!NOTE]
> Azure Data Explorer est associé à une stratégie d’agrégation (traitement par lot) conçue pour optimiser le processus d’ingestion des données.
> Par défaut, la stratégie est configurée sur 5 minutes.
> Vous pouvez modifier la stratégie ultérieurement si nécessaire. Dans cet article, vous pouvez vous attendre à une latence de quelques minutes.

1. Dans le portail Azure, sous votre grille d’événement, vous voyez le pic de l’activité pendant l’exécution de l’application.

    :::image type="content" source="media/ingest-data-event-grid/event-grid-graph.png" alt-text="Graphe d’activité pour la grille d’événement":::

1. Exécutez la requête suivante dans votre base de données de test pour vérifier combien de messages sont arrivés dans la base de données jusqu’à présent.

    ```kusto
    TestTable
    | count
    ```

1. Pour voir le contenu des messages, exécutez la requête suivante dans votre base de données de test.

    ```kusto
    TestTable
    ```

    Le jeu de résultats doit ressembler à l’image suivante :

    :::image type="content" source="media/ingest-data-event-grid/table-result.png" alt-text="Jeu de résultats de message pour Event Grid":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de réutiliser votre grille d’événement, nettoyez l’abonnement Event Grid, l’espace de noms Event Hub et le hub d’événements qui ont été créés automatiquement pour vous, de façon à éviter d’engendrer des coûts.

1. Dans le portail Azure, accédez au menu de gauche et sélectionnez **Toutes les ressources**.

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-all-resource.png" alt-text="Sélectionner toutes les ressources pour le nettoyage de la grille d’événement":::    

1. Recherchez votre espace de noms Event Hub et sélectionnez **Supprimer** pour le supprimer :

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-find-eventhub-namespace-delete.png" alt-text="Nettoyer l’espace de noms Event Hub":::

1. Dans le formulaire Supprimer les ressources, confirmez la suppression pour supprimer l’espace de noms Event Hub et les ressources Event Hub.

1. Accédez à votre compte de stockage. Dans le menu de gauche, sélectionnez **Événements** :

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-events.png" alt-text="Sélectionner les événements à nettoyer pour Event Grid":::

1. Sous le graphe, sélectionnez votre abonnement Event Grid, puis sélectionnez **Supprimer** pour le supprimer :

    :::image type="content" source="media/ingest-data-event-grid/delete-event-grid-subscription.png" alt-text="Supprimer l’abonnement Event Grid":::

1. Pour supprimer votre connexion de données Event Grid, accédez à votre cluster Azure Data Explorer. Dans le menu de gauche, sélectionnez **Bases de données**.

1. Sélectionnez votre base de données **TestDatabase** :

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-database.png" alt-text="Sélectionner la base de données pour nettoyer les ressources":::

1. Dans le menu de gauche, sélectionnez **Ingestion des données** :

    :::image type="content" source="media/ingest-data-event-grid/clean-up-resources-select-data-ingestion.png" alt-text="Sélectionner l’ingestion des données pour nettoyer les ressources":::

1. Sélectionnez votre connexion de données *test-grid-connection*, puis sélectionnez **Supprimer** pour la supprimer.

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans Azure Data Explorer](web-query-data.md)
