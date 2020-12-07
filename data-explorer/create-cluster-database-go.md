---
title: Créer un cluster et une base de données Azure Data Explorer à l’aide du kit SDK Go Azure
description: Découvrez comment créer, lister et supprimer un cluster et une base de données Azure Data Explorer avec le kit SDK Go Azure.
author: orspod
ms.author: orspodek
ms.reviewer: abhishgu
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 833a801e6455fd4d88fbbbab83010aea1d406f02
ms.sourcegitcommit: 7edce9d9d20f9c0505abda67bb8cc3d2ecd60d15
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96524247"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-using-go"></a>Créer un cluster et une base de données Azure Data Explorer à l’aide de Go

> [!div class="op_single_selector"]
> * [Portail](create-cluster-database-portal.md)
> * [INTERFACE DE LIGNE DE COMMANDE](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Go](create-cluster-database-go.md)
> * [Modèle ARM](create-cluster-database-resource-manager.md)

Azure Data Explorer est un service d’analytique données rapide et complètement managé pour l’analyse en temps réel de gros volumes de données diffusées en continu par des applications, des sites web, des appareils IoT, etc. Pour utiliser Azure Data Explorer, créez tout d’abord un cluster et une ou plusieurs bases de données dans ce cluster. Ensuite, ingérez (chargez) des données dans une base de données pour pouvoir exécuter des requêtes dessus. 

Dans cet article, vous allez créer un cluster et une base de données Azure Data Explorer à l’aide de [Go](https://golang.org/). Vous pourrez ensuite lister et supprimer votre nouveau cluster et votre nouvelle base de données, et exécuter des opérations sur vos ressources.

## <a name="prerequisites"></a>Prérequis

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free) avant de commencer.
* Installez [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
* Installez une version appropriée de Go. Pour plus d’informations sur les versions prises en charge, consultez le [kit SDK Go Azure](https://github.com/Azure/azure-sdk-for-go).

## <a name="review-the-code"></a>Vérifier le code

Cette section est facultative. Si vous souhaitez savoir comment le code fonctionne, vous pouvez consulter les extraits de code suivants. Sinon, vous pouvez directement passer à la section [Exécution de l’application](#run-the-application).

### <a name="authentication"></a>Authentification

Le programme doit s’authentifier auprès d’Azure Data Explorer avant d’exécuter des opérations. Le [type d’authentification des informations d’identification du client](/azure/developer/go/azure-sdk-authorization#use-environment-based-authentication) est utilisé par [auth.NewAuthorizerFromEnvironment](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromEnvironment), qui recherche les variables d’environnement prédéfinies suivantes : `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`, `AZURE_TENANT_ID`.

L’exemple suivant montre comment un [kusto.ClustersClient](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go@v48.2.0+incompatible/services/kusto/mgmt/2020-02-15/kusto) est créé à l’aide de cette technique :

```go
func getClustersClient(subscription string) kusto.ClustersClient {
    client := kusto.NewClustersClient(subscription)
    authR, err := auth.NewAuthorizerFromEnvironment()
    if err != nil {
        log.Fatal(err)
    }
    client.Authorizer = authR

    return client
}
```

> [!TIP]
> Utilisez la fonction [auth.NewAuthorizerFromCLIWithResource](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource) pour le développement local si Azure CLI est installé et configuré pour l’authentification.

### <a name="create-cluster"></a>Créer un cluster

Utilisez la fonction [CreateOrUpdate](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto) sur `kusto.ClustersClient` pour créer un cluster Azure Data Explorer. Attendez la fin du processus avant d’examiner les résultats.

```go
func createCluster(sub, name, location, rgName string) {
    ...
    result, err := client.CreateOrUpdate(ctx, rgName, name, kusto.Cluster{Location: &location, Sku: &kusto.AzureSku{Name: kusto.DevNoSLAStandardD11V2, Capacity: &numInstances, Tier: kusto.Basic}})
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
}
```

### <a name="list-clusters"></a>Lister les clusters

Utilisez la fonction [ListByResourceGroup](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClustersClient.ListByResourceGroup) sur `kusto.ClustersClient` pour obtenir un [kusto.ClusterListResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClusterListResult) qui est ensuite itéré pour afficher la sortie dans un format tabulaire.


```go
func listClusters(sub, rgName string) {
    ...
    result, err := getClustersClient(sub).ListByResourceGroup(ctx, rgName)
    ...
    for _, c := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="create-database"></a>Créer une base de données

Utilisez la fonction [CreateOrUpdate](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.CreateOrUpdate) sur [kusto.DatabasesClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient) pour créer une base de données Azure Data Explorer dans un cluster existant. Attendez la fin du processus avant d’examiner les résultats.


```go
func createDatabase(sub, rgName, clusterName, location, dbName string) {
    future, err := client.CreateOrUpdate(ctx, rgName, clusterName, dbName, kusto.ReadWriteDatabase{Kind: kusto.KindReadWrite, Location: &location})
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    ...
}
```

### <a name="list-databases"></a>Lister des bases de données

Utilisez la fonction [ListByCluster](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.ListByCluster) `kusto.DatabasesClient` pour obtenir [kusto.DatabaseListResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabaseListResult), qui est ensuite itéré pour afficher la sortie dans un format tabulaire.


```go
func listDatabases(sub, rgName, clusterName string) {
    result, err := getDBClient(sub).ListByCluster(ctx, rgName, clusterName)
    ...
    for _, db := range *result.Value {
        // setup tabular representation
    }
    ...
}
```

### <a name="delete-database"></a>Supprimer la base de données

Utilisez la fonction [Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#DatabasesClient.Delete) sur un `kusto.DatabasesClient` pour supprimer une base de données existante dans un cluster. Attendez la fin du processus avant d’examiner les résultats.

```go
func deleteDatabase(sub, rgName, clusterName, dbName string) {
    ...
    future, err := getDBClient(sub).Delete(ctx, rgName, clusterName, dbName)
    ...
    err = future.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := future.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

### <a name="delete-cluster"></a>Supprimer un cluster

Utilisez la fonction [Delete](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/kusto/mgmt/2018-09-07-preview/kusto#ClustersClient.Delete) sur un `kusto.ClustersClient` pour supprimer un cluster. Attendez la fin du processus avant d’examiner les résultats.

```go
func deleteCluster(sub, clusterName, rgName string) {
    result, err := client.Delete(ctx, rgName, clusterName)
    ...
    err = result.WaitForCompletionRef(context.Background(), client.Client)
    ...
    r, err := result.Result(client)
    if r.StatusCode == 200 {
        // determine success or failure
    }
    ...
}
```

## <a name="run-the-application"></a>Exécution de l'application

Lorsque vous exécutez l’exemple de code tel quel, les actions suivantes sont effectuées :
    
1. Un cluster Azure Data Explorer est créé.
1. Tous les clusters Azure Data Explorer dans le groupe de ressources spécifié sont listés.
1. Une base de données Azure Data Explorer est créée dans le cluster existant.
1. Toutes les bases de données du cluster spécifié sont listées.
1. La base de données est supprimée.
1. Le cluster est supprimé.


    ```go
    func main() {
        createCluster(subscription, clusterNamePrefix+clusterName, location, rgName)
        listClusters(subscription, rgName)
        createDatabase(subscription, rgName, clusterNamePrefix+clusterName, location, dbNamePrefix+databaseName)
        listDatabases(subscription, rgName, clusterNamePrefix+clusterName)
        deleteDatabase(subscription, rgName, clusterNamePrefix+clusterName, dbNamePrefix+databaseName)
        deleteCluster(subscription, clusterNamePrefix+clusterName, rgName)
    }
    ```

    > [!TIP]
    > Pour tester différentes combinaisons d’opérations, vous pouvez commenter et décommenter les fonctions respectives dans `main.go` selon les besoins.

1. Clonez l’exemple de code depuis GitHub :

    ```console
    git clone https://github.com/Azure-Samples/azure-data-explorer-go-cluster-management.git
    cd azure-data-explorer-go-cluster-management
    ```

1. Le programme s’authentifie à l’aide d’informations d’identification du client. Utilisez la commande Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) pour créer un principal de service. Enregistrez l’ID client, la clé secrète client et les informations sur l’ID de locataire pour les utiliser à l’étape suivante.

1. Exportez les variables d’environnement requises, notamment les informations du principal de service. Entrez votre ID d’abonnement, le groupe de ressources et la région où vous souhaitez créer le cluster.

    ```console
    export AZURE_CLIENT_ID="<enter service principal client ID>"
    export AZURE_CLIENT_SECRET="<enter service principal client secret>"
    export AZURE_TENANT_ID="<enter tenant ID>"

    export SUBSCRIPTION="<enter subscription ID>"
    export RESOURCE_GROUP="<enter resource group name>"
    export LOCATION="<enter azure location e.g. Southeast Asia>"

    export CLUSTER_NAME_PREFIX="<enter prefix (cluster name will be [prefix]-ADXTestCluster)>"
    export DATABASE_NAME_PREFIX="<enter prefix (database name will be [prefix]-ADXTestDB)>"
    ```
    
    > [!TIP]
    > Vous utiliserez sans doute des variables d’environnement dans des scénarios de production pour fournir des informations d’identification à votre application. Comme mentionné dans [Passer en revue le code](#review-the-code), pour le développement local, utilisez [auth.NewAuthorizerFromCLIWithResource](https://pkg.go.dev/github.com/Azure/go-autorest/autorest/azure/auth?tab=doc#NewAuthorizerFromCLIWithResource) si Azure CLI est installé et configuré pour l’authentification. Dans ce cas, vous n’avez pas besoin de créer un principal de service.


1. Exécutez le programme :

    ```console
    go run main.go
    ```

    La sortie ressemblera à ce qui suit :

    ```console
    waiting for cluster creation to complete - fooADXTestCluster
    created cluster fooADXTestCluster
    listing clusters in resource group <your resource group>
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    |       NAME        |  STATE  |    LOCATION    | INSTANCES |                            URI                           |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    | fooADXTestCluster | Running | Southeast Asia |         1 | https://fooADXTestCluster.southeastasia.kusto.windows.net |
    +-------------------+---------+----------------+-----------+-----------------------------------------------------------+
    
    waiting for database creation to complete - barADXTestDB
    created DB fooADXTestCluster/barADXTestDB with ID /subscriptions/<your subscription ID>/resourceGroups/<your resource group>/providers/Microsoft.Kusto/Clusters/fooADXTestCluster/Databases/barADXTestDB and type Microsoft.Kusto/Clusters/Databases
    
    listing databases in cluster fooADXTestCluster
    +--------------------------------+-----------+----------------+------------------------------------+
    |              NAME              |   STATE   |    LOCATION    |                TYPE                |
    +--------------------------------+-----------+----------------+------------------------------------+
    | fooADXTestCluster/barADXTestDB | Succeeded | Southeast Asia | Microsoft.Kusto/Clusters/Databases |
    +--------------------------------+-----------+----------------+------------------------------------+
    
    waiting for database deletion to complete - barADXTestDB
    deleted DB barADXTestDB from cluster fooADXTestCluster

    waiting for cluster deletion to complete - fooADXTestCluster
    deleted ADX cluster fooADXTestCluster from resource group <your resource group>
    ```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez pas supprimé le cluster par programmation à l’aide de l’exemple de code de cet article, supprimez-le manuellement à l’aide d’[Azure CLI](create-cluster-database-cli.md#clean-up-resources).

## <a name="next-steps"></a>Étapes suivantes

[Ingérer des données à l’aide du kit de développement logiciel (SDK) Go Azure Data Explorer](go-ingest-data.md)
