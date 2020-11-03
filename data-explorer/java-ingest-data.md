---
title: Ingérer des données avec le kit SDK Java Azure Data Explorer
description: Dans cet article, vous allez découvrir comment ingérer (charger) des données dans Azure Data Explorer à l’aide du kit SDK Java.
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/22/2020
ms.openlocfilehash: 45b0ce7b1716a4ed2ab7277b6c99a5d95f8ad5af
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906190"
---
# <a name="ingest-data-using-the-azure-data-explorer-java-sdk"></a>Ingérer des données à l’aide du kit SDK Java Azure Data Explorer 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Nœud](node-ingest-data.md)
> * [Go](go-ingest-data.md)
> * [Java](java-ingest-data.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Vous pouvez utiliser la [bibliothèque de client Java](kusto/api/java/kusto-java-client-library.md) pour ingérer des données, émettre des commandes de contrôle et interroger des données dans des clusters Azure Data Explorer.

Dans cet article, vous allez découvrir comment ingérer des données à l’aide de la bibliothèque Java d’Azure Data Explorer. Tout d’abord, vous allez créer une table et un mappage de données dans un cluster de test. Ensuite, vous effectuerez la mise en file d’attente d’une ingestion du stockage d’objets blob vers le cluster à l’aide du kit SDK Java, et vous validerez les résultats.

## <a name="prerequisites"></a>Conditions préalables requises

* Un [compte Azure gratuit](https://azure.microsoft.com/free/)
* [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* JDK version 1.8 ou ultérieure
* [Maven](https://maven.apache.org/download.cgi).
* Un [cluster et une base de données](create-cluster-database-portal.md).
* Créez une [inscription d’application et accordez-lui des autorisations sur la base de données](provision-azure-ad-app.md). Enregistrez l’ID de client et le secret du client pour une utilisation ultérieure.

## <a name="review-the-code"></a>Vérifier le code

Cette section est facultative. Passez en revue les extraits de code suivants pour découvrir comment fonctionne le code. Pour ignorer cette section, accédez à [Exécuter l’application](#run-the-application).

### <a name="authentication"></a>Authentification

Le programme utilise des informations d’identification d’authentification Azure Active Directory avec ConnectionStringBuilder`.

1. Créez un `com.microsoft.azure.kusto.data.Client` pour la requête et la gestion.

    ```java
    static Client getClient() throws Exception {
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(endpoint, clientID, clientSecret, tenantID);
        return ClientFactory.createClient(csb);
    }
    ```

1. Créez et utilisez un `com.microsoft.azure.kusto.ingest.IngestClient` pour la mise en file d’attente de l’ingestion des données dans Azure Data Explorer :

    ```java
    static IngestClient getIngestionClient() throws Exception {
        String ingestionEndpoint = "https://ingest-" + URI.create(endpoint).getHost();
        ConnectionStringBuilder csb = ConnectionStringBuilder.createWithAadApplicationCredentials(ingestionEndpoint, clientID, clientSecret);
        return IngestClientFactory.createClient(csb);
    }
    ```

### <a name="management-commands"></a>Commandes de gestion

Des [commandes de contrôle](kusto/management/commands.md), telles que [`.drop`](kusto/management/drop-function.md) et [`.create`](kusto/management/create-function.md), sont exécutées en appelant `execute` sur un objet `com.microsoft.azure.kusto.data.Client`.

Par exemple, la table `StormEvents` est créée comme suit :

```java
static final String createTableCommand = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)";

static void createTable(String database) {
    try {
        getClient().execute(database, createTableCommand);
        System.out.println("Table created");
    } catch (Exception e) {
        System.out.println("Failed to create table: " + e.getMessage());
        return;
    }
    
}
```

### <a name="data-ingestion"></a>Ingestion de données

Placez l’ingestion en file d’attente à l’aide d’un fichier provenant d’un conteneur Stockage Blob Azure existant. 
* Utilisez `BlobSourceInfo` pour spécifier le chemin du Stockage Blob.
* Utilisez `IngestionProperties` pour définir la table, la base de données, le nom du mappage et le type de données. Dans l’exemple suivant, le type de données est `CSV`.

```java
    ...
    static final String blobPathFormat = "https://%s.blob.core.windows.net/%s/%s%s";
    static final String blobStorageAccountName = "kustosamplefiles";
    static final String blobStorageContainer = "samplefiles";
    static final String fileName = "StormEvents.csv";
    static final String blobStorageToken = "??st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    ....

    static void ingestFile(String database) throws InterruptedException {
        String blobPath = String.format(blobPathFormat, blobStorageAccountName, blobStorageContainer,
                fileName, blobStorageToken);
        BlobSourceInfo blobSourceInfo = new BlobSourceInfo(blobPath);

        IngestionProperties ingestionProperties = new IngestionProperties(database, tableName);
        ingestionProperties.setDataFormat(DATA_FORMAT.csv);
        ingestionProperties.setIngestionMapping(ingestionMappingRefName, IngestionMappingKind.Csv);
        ingestionProperties.setReportLevel(IngestionReportLevel.FailuresAndSuccesses);
        ingestionProperties.setReportMethod(IngestionReportMethod.QueueAndTable);
    ....
```

Le processus d’ingestion démarre dans un thread distinct, et le thread `main` attend que le thread d’ingestion se termine. Ce processus utilise [CountdownLatch](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CountDownLatch.html). L’API d’ingestion (`IngestClient#ingestFromBlob`) n’est pas asynchrone. Une boucle `while` est utilisée pour interroger l’état actuel tous les cinq secondes, et attend que l’état d’ingestion passe de `Pending` à un état différent. L’état final peut être `Succeeded`, `Failed` ou `PartiallySucceeded`.

```java
        ....
        CountDownLatch ingestionLatch = new CountDownLatch(1);
        new Thread(new Runnable() {
            @Override
            public void run() {
                IngestionResult result = null;
                try {
                    result = getIngestionClient().ingestFromBlob(blobSourceInfo, ingestionProperties);
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
                try {
                    IngestionStatus status = result.getIngestionStatusCollection().get(0);
                    while (status.status == OperationStatus.Pending) {
                        Thread.sleep(5000);
                        status = result.getIngestionStatusCollection().get(0);
                    }
                    ingestionLatch.countDown();
                } catch (Exception e) {
                    ingestionLatch.countDown();
                }
            }
        }).start();
        ingestionLatch.await();
    }
```

> [!TIP]
> Il existe d’autres méthodes pour gérer l’ingestion asynchrone pour différentes applications. Par exemple, vous pouvez utiliser [`CompletableFuture`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html) pour créer un pipeline définissant l’action post-ingestion, telle que l’interrogation de la table, ou pour gérer les exceptions qui ont été signalées à `IngestionStatus`.

## <a name="run-the-application"></a>Exécution de l'application

### <a name="general"></a>Général

Lorsque vous exécutez l’exemple de code, les actions suivantes sont effectuées :

   1. **Supprimer une table**  : la table `StormEvents` est supprimée (si elle existe).
   1. **Création de la table**  : la table `StormEvents` est créée.
   1. **Création du mappage**  : le mappage `StormEvents_CSV_Mapping` est créé.
   1. **Ingestion de fichiers**  : un fichier CSV (dans le Stockage Blob Azure) est mis en file d’attente à des fins d’ingestion.

L’exemple de code suivant provient de `App.java` :

```java
public static void main(final String[] args) throws Exception {
    dropTable(database);
    createTable(database);
    createMapping(database);
    ingestFile(database);
}
```

> [!TIP]
> Pour tester différentes combinaisons d’opérations, commentez/décommentez les méthodes respectives dans `App.java`.

### <a name="run-the-application"></a>Exécution de l'application

1. Clonez l’exemple de code depuis GitHub :

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-java-sdk-ingest.git
    cd azure-data-explorer-java-sdk-ingest
    ```

1. Définissez les informations du principal de service avec les informations suivantes en tant que variables d’environnement utilisées par le programme :
    * Point de terminaison de cluster 
    * Nom de la base de données 

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. Générez et exécutez :

    ```console
    mvn clean package
    java -jar target/adx-java-ingest-jar-with-dependencies.jar
    ```

    La sortie doit ressembler à ceci :

    ```console
    Table dropped
    Table created
    Mapping created
    Waiting for ingestion to complete...
    ```

Attendez quelques minutes que le processus d’ingestion se termine. Une fois l’opération terminée, le message de journal suivant s’affiche : `Ingestion completed successfully`. Vous pouvez quitter le programme à ce stade et passer à l’étape suivante sans affecter le processus d’ingestion, qui a déjà été mis en file d’attente.

## <a name="validate"></a>Valider

Attendez cinq à dix minutes que l’ingestion en file d’attente planifie le processus d’ingestion et charge les données dans Azure Data Explorer. 

1. Connectez-vous à [https://dataexplorer.azure.com](https://dataexplorer.azure.com) et à votre cluster. 
1. Exécutez la commande suivante pour obtenir le nombre d’enregistrements de la table `StormEvents` :
    
    ```kusto
    StormEvents | count
    ```

## <a name="troubleshoot"></a>Dépanner

1. Pour voir les échecs d’ingestion des quatre dernières heures, exécutez la commande suivante sur votre base de données : 

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. Pour voir l’état de toutes les opérations d’ingestion des quatre dernières heures, exécutez la commande suivante :

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne prévoyez pas d’utiliser les ressources que vous venez de créer, exécutez la commande suivante dans votre base de données pour supprimer la table `StormEvents`.

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Étapes suivantes

[Écrire des requêtes](write-queries.md)
