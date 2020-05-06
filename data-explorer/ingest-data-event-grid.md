---
title: Ingérer des objets blob Azure dans Azure Data Explorer
description: Cet article vous montre comment envoyer des données de compte de stockage à Azure Data Explorer en utilisant un abonnement Event Grid.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 69779e42f14a1dfc512d8752f2cc9989897b2cc6
ms.sourcegitcommit: 4f68d6dbfa6463dbb284de0aa17fc193d529ce3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82741984"
---
# <a name="ingest-blobs-into-azure-data-explorer-by-subscribing-to-event-grid-notifications"></a>Ingérer des objets blob dans Azure Data Explorer en s’abonnant à des notifications Event Grid

> [!div class="op_single_selector"]
> * [Portail](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Modèle Azure Resource Manager](data-connection-event-grid-resource-manager.md)

Azure Data Explorer est un service d’exploration de données rapide et évolutif pour les données de journal et de télémétrie. Il assure une ingestion continue (chargement de données) à partir d’objets blob écrits dans des conteneurs d’objets blob. 

Cet article vous montre comment définir un abonnement [Azure Event Grid](/azure/event-grid/overview) et acheminer les événements vers Azure Data Explorer via un Event Hub. Pour commencer, vous devez avoir un compte de stockage avec un abonnement Event Grid qui envoie des notifications à Azure Event Hubs. Ensuite, vous créez une connexion de données Event Grid et voyez les données circuler dans le système.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Créez un [compte Azure gratuit](https://azure.microsoft.com/free/).
* [Un cluster et une base de données](create-cluster-database-portal.md).
* [Un compte de stockage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).
* [Un Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

## <a name="create-an-event-grid-subscription-in-your-storage-account"></a>Créer un abonnement Event Grid dans votre compte de stockage

1. Dans le portail Azure, recherchez votre compte de stockage.
1. Sélectionnez **Événements** > **Abonnement aux événements**.

    ![Lien d’application de requête](media/ingest-data-event-grid/create-event-grid-subscription.png)

1. Dans la fenêtre **Créer un abonnement aux événements** sous l’onglet **De base**, spécifiez les valeurs suivantes :

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom | *test-grid-connection* | Nom de la grille d’événement que vous voulez créer.|
    | Schéma d’événement | *Schéma Event Grid* | Schéma à utiliser pour la grille d’événement. |
    | Type de rubrique | *Compte de stockage* | Type de rubrique Event Grid. |
    | Ressource de rubrique | *gridteststorage* | nom de votre compte de stockage. |
    | S’abonner à tous les types d’événements | *clear* | Ne pas être notifié de tous les événements. |
    | Types d’événements définis | *Objet blob créé* | De quels événements spécifiques être notifié. |
    | Type de point de terminaison | *Event Hubs* | Type de point de terminaison auquel vous envoyez les événements. |
    | Point de terminaison | *test-hub* | Hub d’événements que vous avez créé. |
    | | |

1. Sélectionnez l’onglet **Filtres** si vous voulez effectuer le suivi des fichiers d’un conteneur spécifique. Définissez les filtres pour les notifications comme suit :
    * Le champ **Le sujet commence par** est le préfixe *littéral* du conteneur d’objets blob. Comme le modèle appliqué est *startswith*, il peut s’étendre sur plusieurs conteneurs. Les caractères génériques ne sont pas autorisés.
        * Pour définir un filtre sur le conteneur d’objets blob, le champ *doit* être défini comme suit : *`/blobServices/default/containers/[container prefix]`* . 
        * Pour définir un filtre sur un préfixe d’objet blob (ou un dossier dans ADLS gen2), le champ *doit* être défini comme suit : *`/blobServices/default/containers/[container name]/blobs/[folder/blob prefix]`* . 
    * Le champ **Le sujet se termine par** est le suffixe *littéral* de l’objet blob. Les caractères génériques ne sont pas autorisés.
    * Pour plus d’informations sur le filtrage des événements, consultez [Événements du stockage Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview#filtering-events). 
    
## <a name="create-a-target-table-in-azure-data-explorer"></a>Créer une table cible dans l’Explorateur de données Azure

Créez une table dans Azure Data Explorer, à laquelle Event Hubs enverra les données. Créez la table dans le cluster et la base de données préparés dans en lien avec les conditions préalables.

1. Dans le portail Azure, sous votre cluster, sélectionnez **Requête**.

    ![Lien d’application de requête](media/ingest-data-event-grid/query-explorer-link.png)

1. Copiez la commande suivante dans la fenêtre, puis sélectionnez **Exécuter** pour créer la table (TestTable) qui doit recevoir les données ingérées.

    ```kusto
    .create table TestTable (TimeStamp: datetime, Value: string, Source:string)
    ```

    ![Exécuter la requête de création](media/ingest-data-event-grid/run-create-table.png)

1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter** pour mapper les données JSON entrantes aux types de données et aux noms de colonne de la table (TestTable).

    ```kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.TimeStamp"},{"column":"Value","path":"$.Value"},{"column":"Source","path":"$.Source"}]'
    ```

## <a name="create-an-event-grid-data-connection-in-azure-data-explorer"></a>Créer une connexion de données Event Grid dans Azure Data Explorer

Établissez à présent une connexion à Event Grid à partir d’Azure Data Explorer, afin que les données circulant dans le conteneur d’objets blob soient envoyées en streaming à la table de test. 

1. Sélectionnez **Notifications** dans la barre d’outils pour vérifier que le déploiement du hub d’événements a réussi.

1. Sous le cluster que vous avez créé, sélectionnez **Bases de données** > **TestDatabase**.

    ![Sélectionner la base de données de test](media/ingest-data-event-grid/select-test-database.png)

1. Sélectionnez **Ingestion des données** > **Ajouter la connexion de données**.

    ![Ingestion de données](media/ingest-data-event-grid/data-ingestion-create.png)

1.  Sélectionnez le type de connexion : **Stockage d'objets blob**.

1. Renseignez le formulaire avec les informations suivantes, puis sélectionnez **Créer**.

    ![Connexion du hub d’événements](media/ingest-data-event-grid/create-event-grid-data-connection.png)

     Source de données :

    **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Nom de la connexion de données | *test-hub-connection* | Nom de la connexion que vous souhaitez créer dans Azure Data Explorer.|
    | Abonnement du compte de stockage | Votre ID d’abonnement | ID d’abonnement où se trouve votre compte de stockage.|
    | Compte de stockage | *gridteststorage* | Nom du compte de stockage que vous avez créé précédemment.|
    | Event Grid | *test-grid-connection* | Nom de la grille d’événement que vous avez créée. |
    | Nom de l’Event Hub | *test-hub* | Event Hub que vous avez créé. Ce champ est automatiquement renseigné quand vous sélectionnez une grille d’événement. |
    | Groupe de consommateurs | *test-group* | Groupe de consommateurs défini dans l’Event Hub que vous avez créé. |
    | | |

    Table cible :

     **Paramètre** | **Valeur suggérée** | **Description du champ**
    |---|---|---|
    | Table de charge de travail | *TestTable* | Table que vous avez créée dans **TestDatabase**. |
    | Format de données | *JSON* | Les formats pris en charge sont Avro, CSV, JSON, MULTILINE JSON, PSV, SOH, SCSV, TSV, RAW et TXT. Options de compression prises en charge : ZIP et GZIP |
    | Mappage de colonnes | *TestMapping* | Le mappage que vous avez créé dans **TestDatabase**, qui mappe les données JSON entrantes dans les colonnes des noms de colonne et les types de données de **TestTable**.|
    | | |
    
## <a name="generate-sample-data"></a>Générer un exemple de données

Maintenant qu’Azure Data Explorer et le compte de stockage sont connectés, vous pouvez créer des exemples de données et les charger dans le stockage d’objets blob.

Nous allons maintenant utiliser un petit script de shell qui exécute quelques commandes Azure CLI de base pour interagir avec les ressources de Stockage Azure. Tout d’abord, le script crée un conteneur dans votre compte de stockage, télécharge un fichier existant (tel qu’objet blob) dans ce conteneur, puis affiche la liste des objets blob dans le conteneur. Vous pouvez utiliser [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) pour exécuter le script directement dans le portail.

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

Vous pouvez spécifier les [propriétés d’ingestion](ingestion-properties.md) de l’ingestion d’objets blob via les métadonnées d’objet blob.

Les propriétés suivantes peuvent être définies :

|**Propriété** | **Description de la propriété**|
|---|---|
| `rawSizeBytes` | Taille des données brutes (non compressées). Pour Avro/ORC/Parquet, il s’agit de la taille avant l’application de la compression spécifique au format.|
| `kustoTable` |  Nom de la table cible existante. Remplace le paramètre `Table` défini dans le panneau `Data Connection`. |
| `kustoDataFormat` |  Format de données. Remplace le paramètre `Data format` défini dans le panneau `Data Connection`. |
| `kustoIngestionMappingReference` |  Nom du mappage d’ingestion existant à utiliser. Remplace le paramètre `Column mapping` défini dans le panneau `Data Connection`.|
| `kustoIgnoreFirstRecord` | Si la valeur définie est `true`, Kusto ignore la première ligne de l’objet blob. Utilisez dans les données au format tabulaire (CSV, TSV ou similaire) pour ignorer les en-têtes. |
| `kustoExtentTags` | Chaîne représentant les [balises](kusto/management/extents-overview.md#extent-tagging) qui seront attachées à l’étendue résultante. |
| `kustoCreationTime` |  Remplace [$IngestionTime](kusto/query/ingestiontimefunction.md?pivots=azuredataexplorer) pour l’objet blob, au format d’une chaîne ISO 8601. À utiliser pour le renvoi. |

> [!NOTE]
> Azure Data Explorer ne supprimera pas les objets blob après l’ingestion.
> Conservez les objets blob pendant trois à cinq jours.
> Pour gérer la suppression des objets blob, utilisez le [cycle de vie du stockage Blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal). 

## <a name="review-the-data-flow"></a>Examiner le flux de données

> [!NOTE]
> Azure Data Explorer est associé à une stratégie d’agrégation (traitement par lot) conçue pour optimiser le processus d’ingestion des données.
Par défaut, la stratégie est configurée sur 5 minutes.
Vous pouvez modifier la stratégie ultérieurement si nécessaire. Dans cet article, vous pouvez vous attendre à une latence de quelques minutes.

1. Dans le portail Azure, sous votre grille d’événement, vous voyez le pic de l’activité pendant l’exécution de l’application.

    ![Graphe de la grille d’événement](media/ingest-data-event-grid/event-grid-graph.png)

1. Exécutez la requête suivante dans votre base de données de test pour vérifier combien de messages sont arrivés dans la base de données jusqu’à présent.

    ```kusto
    TestTable
    | count
    ```

1. Pour voir le contenu des messages, exécutez la requête suivante dans votre base de données de test.

    ```kusto
    TestTable
    ```

    Le jeu de résultats doit ressembler à ce qui suit.

    ![Jeu de résultats des messages](media/ingest-data-event-grid/table-result.png)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas de réutiliser votre grille d’événement, effacez **test-hub-rg** pour éviter des frais.

1. Dans le portail Azure, sélectionnez **Groupes de ressources** tout à gauche, puis sélectionnez le groupe de ressources que vous avez créé.  

    Si le menu de gauche est réduit, sélectionnez le ![Bouton Développer](media/ingest-data-event-grid/expand.png) pour le développer.

   ![Sélectionner un groupe de ressources à supprimer](media/ingest-data-event-grid/delete-resources-select.png)

1. Sous **test-resource-group**, sélectionnez **Supprimer le groupe de ressources**.

1. Dans la nouvelle fenêtre, entrez le nom du groupe de ressources à supprimer (*test-hub-rg*), puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* [Interroger des données dans Azure Data Explorer](web-query-data.md)
