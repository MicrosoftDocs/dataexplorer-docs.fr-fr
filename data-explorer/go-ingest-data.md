---
title: Ingérer des données avec le kit de développement logiciel (SDK) Go Azure Data Explorer
description: Dans cet article, vous apprendrez comment ingérer (charger) des données dans Azure Data Explorer à l’aide kit de développement logiciel (SDK) Go.
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/10/2020
ms.openlocfilehash: c133c3cf1185e7ffdb959ed6ea127af7502820c4
ms.sourcegitcommit: 898f67b83ae8cf55e93ce172a6fd3473b7c1c094
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92342567"
---
# <a name="ingest-data-using-the-azure-data-explorer-go-sdk"></a>Ingérer des données à l’aide du kit de développement logiciel (SDK) Go Azure Data Explorer 

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Nœud](node-ingest-data.md)
> * [Go](go-ingest-data.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Il fournit une [bibliothèque de client de kit de développement logiciel (SDK) Go](kusto/api/golang/kusto-golang-client-library.md) à des fins d’interaction avec le service Azure Data Explorer. Vous pouvez utiliser le [kit de développement logiciel (SDK) Go](https://github.com/Azure/azure-kusto-go) pour ingérer, contrôler et interroger les données des clusters Azure Data Explorer. 

Dans cet article, vous allez d’abord créer une table et un mappage de données dans un cluster de test. Vous mettez ensuite l’ingestion en file d’attente l’ingestion sur le cluster à l’aide du kit de développement logiciel (SDK) Go et validez les résultats.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Installez [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installez [Go](https://golang.org/) en suivant la configuration minimale requise pour le [kit de développement logiciel (SDK) Go](kusto/api/golang/kusto-golang-client-library.md#minimum-requirements). 
* Créez [un cluster et une base de données Azure Data Explorer](create-cluster-database-portal.md).
* Créez une [inscription d’application et accordez-lui des autorisations sur la base de données](provision-azure-ad-app.md). Enregistrez l’ID de client et le secret du client pour une utilisation ultérieure.

## <a name="install-the-go-sdk"></a>Installer le kit de développement logiciel (SDK) Go

Le kit de développement logiciel (SDK) Go Azure Data Explorer est automatiquement installé lorsque vous exécutez l’exemple d’application qui utilise les [modules Go](https://golang.org/ref/mod). Si vous avez installé le kit de développement logiciel (SDK) Go pour une autre application, créez un module Go et récupérez le package Azure Data Explorer (en utilisant `go get`), par exemple :

```console
go mod init foo.com/bar
go get github.com/Azure/azure-kusto-go/kusto
```

La dépendance du package est ajoutée au fichier `go.mod`. Utilisez-le dans votre application

## <a name="review-the-code"></a>Vérifier le code

Cette section [Révision du code](#review-the-code) est facultative. Si vous souhaitez savoir comment le code fonctionne, vous pouvez consulter les extraits de code suivants. Sinon, vous pouvez directement passer à la section [Exécution de l’application](#run-the-application).

### <a name="authenticate"></a>Authenticate

Le programme doit s’authentifier auprès du service Azure Data Explorer avant d’exécuter des opérations.

```go
auth := kusto.Authorization{Config: auth.NewClientCredentialsConfig(clientID, clientSecret, tenantID)}
client, err := kusto.New(kustoEndpoint, auth)
```

Une instance d’[autorisation Kusto](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Authorization) est créée à l’aide des informations d’identification du principal de service. Elle est ensuite utilisée pour créer un client [kusto.Client](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client) avec la fonction [Nouveau](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#New]) qui accepte également le point de terminaison du cluster.

### <a name="create-table"></a>Créer une table

La commande create table est représentée par une [instruction Kusto](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Stmt). La fonction [Mgmt](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#Client.Mgmt) est utilisée pour exécuter des commandes de gestion. Elle permet d’exécuter la commande afin de créer une table. 

```go
func createTable(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createTableCommand))
    if err != nil {
        log.Fatal("failed to create table", err)
    }
    log.Printf("Table %s created in DB %s\n", kustoTable, kustoDB)
}
```

> [!TIP]
> Par défaut, une instruction Kusto est constante à des fins de sécurité renforcée. [`NewStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#NewStmt) accepte les constantes de chaînes. L’API [`UnsafeStmt`](https://godoc.org/github.com/Azure/azure-kusto-go/kusto#UnsafeStmt) autorise l’utilisation de segments d’instruction non constants, mais ce n’est pas conseillé.

La commande Kusto create table est la suivante :

```kusto
.create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
```

### <a name="create-mapping"></a>Créer un mappage

Les mappages de données sont utilisés lors de l’ingestion pour mapper les données entrantes avec les colonnes des tables Azure Data Explorer. Pour plus d’informations, consultez les [Mappage de données](kusto/management/mappings.md). Le mappage est créé de la même façon qu’une table, à l’aide de la fonction `Mgmt`, avec le nom de la base de données et la commande appropriée. La commande complète est disponible dans le [référentiel GitHub correspondant à l’exemple](https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data/blob/main/main.go#L20).

```go
func createMapping(kc *kusto.Client, kustoDB string) {
    _, err := kc.Mgmt(context.Background(), kustoDB, kusto.NewStmt(createMappingCommand))
    if err != nil {
        log.Fatal("failed to create mapping - ", err)
    }
    log.Printf("Mapping %s created\n", kustoMappingRefName)
}
```

### <a name="ingest-data"></a>Réception de données

Une ingestion est mise en file d’attente à l’aide d’un fichier provenant d’un conteneur Stockage Blob Azure existant. 

```go
func ingestFile(kc *kusto.Client, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable string) {
    kIngest, err := ingest.New(kc, kustoDB, kustoTable)
    if err != nil {
        log.Fatal("failed to create ingestion client", err)
    }
    blobStorePath := fmt.Sprintf(blobStorePathFormat, blobStoreAccountName, blobStoreContainer, blobStoreFileName, blobStoreToken)
    err = kIngest.FromFile(context.Background(), blobStorePath, ingest.FileFormat(ingest.CSV), ingest.IngestionMappingRef(kustoMappingRefName, ingest.CSV))

    if err != nil {
        log.Fatal("failed to ingest file", err)
    }
    log.Println("Ingested file from -", blobStorePath)
}
```

Le client [Ingestion](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion) est créé à l’aide de [ingest.New](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#New). La fonction [FromFile](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#Ingestion.FromFile) est utilisée pour faire référence à l’URI du Stockage Blob Azure. Le nom de la référence de mappage et le type de données sont transmis sous la forme de [FileOption](https://godoc.org/github.com/Azure/azure-kusto-go/kusto/ingest#FileOption). 

## <a name="run-the-application"></a>Exécution de l'application

1. Clonez l’exemple de code depuis GitHub :

    ```console
    git clone https://github.com/Azure-Samples/Azure-Data-Explorer-Go-SDK-example-to-ingest-data.git
    cd Azure-Data-Explorer-Go-SDK-example-to-ingest-data
    ```

1. Exécutez l’exemple de code comme indiqué dans cet extrait de code depuis `main.go` : 

    ```go
    func main {
        ...
        dropTable(kc, kustoDB)
        createTable(kc, kustoDB)
        createMapping(kc, kustoDB)
        ingestFile(kc, blobStoreAccountName, blobStoreContainer, blobStoreToken, blobStoreFileName, kustoMappingRefName, kustoDB, kustoTable)
        ...
    }
    ```

    > [!TIP]
    > Pour tester différentes combinaisons d’opérations, vous pouvez supprimer les marques de commentaire des fonctions qui conviennent dans `main.go`.

    Lorsque vous exécutez l’exemple de code, les actions suivantes sont effectuées :
    
    1. **Supprimer une table**  : la table `StormEvents` est supprimée (si elle existe).
    1. **Création de la table**  : la table `StormEvents` est créée.
    1. **Création du mappage**  : le mappage `StormEvents_CSV_Mapping` est créé.
    1. **Ingestion de fichiers**  : un fichier CSV (dans le Stockage Blob Azure) est mis en file d’attente à des fins d’ingestion.

1. Pour créer un principal de service pour l’authentification, utilisez Azure CLI avec la commande [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac). Définissez les informations du principal de service avec le point de terminaison du cluster et le nom de la base de données sous forme de variables d’environnement qui seront utilisées par le programme :

    ```console
    export AZURE_SP_CLIENT_ID="<replace with appID>"
    export AZURE_SP_CLIENT_SECRET="<replace with password>"
    export AZURE_SP_TENANT_ID="<replace with tenant>"
    export KUSTO_ENDPOINT="https://<cluster name>.<azure region>.kusto.windows.net"
    export KUSTO_DB="name of the database"
    ```

1. Exécutez le programme :

    ```console
    go run main.go
    ```

    La sortie se présente comme suit :

    ```console
    Connected to Azure Data Explorer
    Using database - testkustodb
    Failed to drop StormEvents table. Maybe it does not exist?
    Table StormEvents created in DB testkustodb
    Mapping StormEvents_CSV_Mapping created
    Ingested file from - https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D
    ```

## <a name="validate-and-troubleshoot"></a>Valider et résoudre les problèmes

Attendez 5 à 10 minutes avant que l’ingestion en file d’attente planifie le processus d’ingestion et charge les données dans Azure Data Explorer. 

1. Connectez-vous à [https://dataexplorer.azure.com](https://dataexplorer.azure.com) et à votre cluster. Exécutez ensuite la commande suivante pour obtenir le nombre d’enregistrements de la table `StormEvents`.

    ```kusto
    StormEvents | count
    ```

2. Exécutez la commande suivante dans votre base de données pour voir si des échecs d’ingestion se sont produits ces quatre dernières heures. Remplacez le nom de la base de données avant l’exécution.

    ```kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
    ```

1. Exécutez la commande suivante pour voir l’état de toutes les opérations d’ingestion des quatre dernières heures. Remplacez le nom de la base de données avant l’exécution.

    ```kusto
    .show operations
    | where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
    | summarize arg_max(LastUpdatedOn, *) by OperationId
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de suivre nos autres articles, conservez les ressources que vous avez créées. Sinon, exécutez la commande suivante dans votre base de données pour supprimer la table `StormEvents`.

```kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Étapes suivantes

[Écrire des requêtes](write-queries.md)