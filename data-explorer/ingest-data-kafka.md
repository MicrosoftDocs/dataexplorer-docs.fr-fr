---
title: Ingérer des données de Kafka dans Azure Data Explorer
description: Dans cet article, vous allez apprendre à ingérer (charger) des données dans Azure Data Explorer depuis Kafka.
author: orspod
ms.author: orspodek
ms.reviewer: ankhanol
ms.service: data-explorer
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 14f4ed38ecb2e5b4a94dad8a73fb43ea3ff1e5ee
ms.sourcegitcommit: c8256390d745e345f44d401e33e775702d62721d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "91007792"
---
# <a name="ingest-data-from-apache-kafka-into-azure-data-explorer"></a>Ingérer des données Apache Kafka dans Azure Data Explorer
 
Azure Data Explorer prend en charge l’[ingestion de données](ingest-data-overview.md) d’[Apache Kafka](http://kafka.apache.org/documentation/). Apache Kafka est une plateforme de streaming distribuée qui permet la création de pipelines de données de streaming en temps réel, qui déplacent les données de façon fiable entre des systèmes ou des applications. [Kafka Connect](https://docs.confluent.io/3.0.1/connect/intro.html#kafka-connect) est un outil pour le streaming de données scalable et fiable entre Apache Kafka et d’autres systèmes de données. Le [récepteur Kafka](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) Azure Data Explorer sert de connecteur à partir de Kafka et ne demande aucune utilisation de code. Téléchargez le fichier jar du connecteur de récepteur depuis ce [dépôt Git](https://github.com/Azure/kafka-sink-azure-kusto/releases) ou depuis [Confluent Connector Hub](https://www.confluent.io/hub/microsoftcorporation/kafka-sink-azure-kusto).

Cet article montre comment ingérer des données avec Kafka dans Azure Data Explorer en utilisant une configuration Docker autonome pour simplifier la configuration du cluster Kafka et du cluster de connecteur Kafka. 

Pour plus d’informations, consultez le [dépôt Git](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md) et les [spécificités des versions](https://github.com/Azure/kafka-sink-azure-kusto/blob/master/README.md#13-major-version-specifics) du connecteur.

## <a name="prerequisites"></a>Prérequis

* Créez un [compte Microsoft Azure](https://docs.microsoft.com/azure/).
* Installez [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).
* Installez [Docker](https://docs.docker.com/get-docker/) et [Docker Compose](https://docs.docker.com/compose/install).
* [Créez une base de données et un cluster Azure Data Explorer dans le portail Azure](create-cluster-database-portal.md) en utilisant les stratégies de conservation et le cache par défaut.

## <a name="create-an-azure-active-directory-service-principal"></a>Créer un principal du service Azure Active Directory

Le principal du service Azure Active Directory peut être créé dans le [portail Azure](/azure/active-directory/develop/howto-create-service-principal-portal) ou programmatiquement, comme dans l’exemple suivant.

Ce principal de service sera l’identité exploitée par le connecteur pour écrire dans la table Azure Data Explorer. Nous accorderons ultérieurement des autorisations pour ce principal du service afin d’accéder à Azure Data Explorer.

1. Connectez-vous à votre abonnement Azure via Azure CLI. Authentifiez-vous ensuite dans le navigateur.

   ```azurecli-interactive
   az login
   ```


1. Choisissez l’abonnement que vous voulez utiliser pour exécuter le labo. Cette étape est nécessaire quand vous avez plusieurs abonnements.

   ```azurecli-interactive
   az account set --subscription YOUR_SUBSCRIPTION_GUID
   ```

1. Créez le principal de service. Dans cet exemple, le principal de service est appelé `kusto-kafka-spn`.

   ```azurecli-interactive
   az ad sp create-for-rbac -n "kusto-kafka-spn"
   ```

1. Vous obtiendrez une réponse JSON comme suit. Copiez l’`appId`, le `password` et le `tenant` parce que vous en aurez besoin dans les étapes ultérieures.

    ```json
    {
      "appId": "fe7280c7-5705-4789-b17f-71a472340429",
      "displayName": "kusto-kafka-spn",
      "name": "http://kusto-kafka-spn",
      "password": "29c719dd-f2b3-46de-b71c-4004fb6116ee",
      "tenant": "42f988bf-86f1-42af-91ab-2d7cd011db42"
    }
    ```

## <a name="create-a-target-table-in-azure-data-explorer"></a>Créer une table cible dans l’Explorateur de données Azure

1. Connectez-vous au [portail Azure](https://portal.azure.com)

1. Accédez à votre cluster Azure Data Explorer.

1. Créez une table appelée `Storms` en utilisant la commande suivante :

    ```kusto
    .create table Storms (StartTime: datetime, EndTime: datetime, EventId: int, State: string, EventType: string, Source: string)
    ```

    :::image type="content" source="media/ingest-data-kafka/create-table.png" alt-text="Créer une table dans le portail Azure Data Explorer ":::
    
1. Créez le mappage de table correspondant `Storms_CSV_Mapping` pour les données ingérées en utilisant la commande suivante :
    
    ```kusto
    .create table Storms ingestion csv mapping 'Storms_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EventId","datatype":"int","Ordinal":2},{"Name":"State","datatype":"string","Ordinal":3},{"Name":"EventType","datatype":"string","Ordinal":4},{"Name":"Source","datatype":"string","Ordinal":5}]'
    ```    

1. Créez une stratégie d’ingestion par lot sur la table pour la latence d’ingestion configurable.

    > [!TIP]
    > La [stratégie d’ingestion par lot](kusto/management/batchingpolicy.md) est un optimiseur de performances et comprend trois paramètres. Le premier paramètre rencontré déclenche l’ingestion dans la table Azure Data Explorer.

    ```kusto
    .alter table Storms policy ingestionbatching @'{"MaximumBatchingTimeSpan":"00:00:15", "MaximumNumberOfItems": 100, "MaximumRawDataSizeMB": 300}'
    ```

1. Utilisez le principal de service de [Créer un principal de service Azure Active Directory](#create-an-azure-active-directory-service-principal) pour accorder l’autorisation d’utiliser la base de données.

    ```kusto
    .add database YOUR_DATABASE_NAME admins  ('aadapp=YOUR_APP_ID;YOUR_TENANT_ID') 'AAD App'
    ```

## <a name="run-the-lab"></a>Exécuter le labo

Le labo suivant est conçu pour vous permettre de commencer à créer des données, à configurer le connecteur Kafka et à diffuser en streaming ces données vers Azure Data Explorer avec le connecteur. Vous pouvez ensuite examiner les données ingérées.

### <a name="clone-the-git-repo"></a>Cloner le dépôt Git

Clonez le [dépôt](https://github.com/Azure/azure-kusto-labs) Git du labo.

1. Créez un répertoire local sur votre machine.

    ```
    mkdir ~/kafka-kusto-hol
    cd ~/kafka-kusto-hol
    ```

1. Clonez le dépôt.

    ```shell
    cd ~/kafka-kusto-hol
    git clone https://github.com/Azure/azure-kusto-labs
    cd azure-kusto-labs/kafka-integration/dockerized-quickstart
    ```

#### <a name="contents-of-the-cloned-repo"></a>Contenu du dépôt cloné

Exécutez la commande suivante pour lister le contenu du dépôt cloné :

```
cd ~/kafka-kusto-hol/azure-kusto-labs/kafka-integration/dockerized-quickstart
tree
```

Le résultat de cette recherche est le suivant :

```
├── README.md
├── adx-query.png
├── adx-sink-config.json
├── connector
│   └── Dockerfile
├── docker-compose.yaml
└── storm-events-producer
    ├── Dockerfile
    ├── StormEvents.csv
    ├── go.mod
    ├── go.sum
    ├── kafka
    │   └── kafka.go
    └── main.go
 ```

### <a name="review-the-files-in-the-cloned-repo"></a>Examiner les fichiers dans le dépôt cloné

Les sections suivantes décrivent les parties importantes des fichiers de l’arborescence de fichiers ci-dessus.

#### <a name="adx-sink-configjson"></a>adx-sink-config.json

Ce fichier contient le fichier de propriétés du récepteur Kusto dans lequel vous allez mettre à jour des détails de configuration spécifiques :
 
```json
{
    "name": "storm",
    "config": {
        "connector.class": "com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector",
        "flush.size.bytes": 10000,
        "flush.interval.ms": 10000,
        "tasks.max": 1,
        "topics": "storm-events",
        "kusto.tables.topics.mapping": "[{'topic': 'storm-events','db': '<enter database name>', 'table': 'Storms','format': 'csv', 'mapping':'Storms_CSV_Mapping'}]",
        "aad.auth.authority": "<enter tenant ID>",
        "aad.auth.appid": "<enter application ID>",
        "aad.auth.appkey": "<enter client secret>",
        "kusto.url": "https://ingest-<name of cluster>.<region>.kusto.windows.net",
        "key.converter": "org.apache.kafka.connect.storage.StringConverter",
        "value.converter": "org.apache.kafka.connect.storage.StringConverter"
    }
}
```

Remplacez les valeurs des attributs suivants par celles de votre configuration Azure Data Explorer : `aad.auth.authority`, `aad.auth.appid`, `aad.auth.appkey`, `kusto.tables.topics.mapping` (nom de la base de données) et `kusto.url`.

#### <a name="connector---dockerfile"></a>Connector - Dockerfile

Ce fichier contient les commandes permettant de générer l’image Docker pour l’instance du connecteur.  Il comprend le téléchargement du connecteur à partir du répertoire de publication des dépôts git.

#### <a name="storm-events-producer-directory"></a>Répertoire Storm-events-producer

Ce répertoire contient un programme Go qui lit un fichier local « StormEvents. csv » et publie les données dans une rubrique Kafka.

#### <a name="docker-composeyaml"></a>docker-compose.yaml

```yaml
version: "2"
services:
  zookeeper:
    image: debezium/zookeeper:1.2
    ports:
      - 2181:2181
  kafka:
    image: debezium/kafka:1.2
    ports:
      - 9092:9092
    links:
      - zookeeper
    depends_on:
      - zookeeper
    environment:
      - ZOOKEEPER_CONNECT=zookeeper:2181
      - KAFKA_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
  kusto-connect:
    build:
      context: ./connector
      args:
        KUSTO_KAFKA_SINK_VERSION: 1.0.1
    ports:
      - 8083:8083
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - BOOTSTRAP_SERVERS=kafka:9092
      - GROUP_ID=adx
      - CONFIG_STORAGE_TOPIC=my_connect_configs
      - OFFSET_STORAGE_TOPIC=my_connect_offsets
      - STATUS_STORAGE_TOPIC=my_connect_statuses
  events-producer:
    build:
      context: ./storm-events-producer
    links:
      - kafka
    depends_on:
      - kafka
    environment:
      - KAFKA_BOOTSTRAP_SERVER=kafka:9092
      - KAFKA_TOPIC=storm-events
      - SOURCE_FILE=StormEvents.csv
```

### <a name="start-the-containers"></a>Démarrer les conteneurs

1. Sur un terminal, démarrez les conteneurs :
    
    ```shell
    docker-compose up
    ```

    L’application producer commence à envoyer des événements à la rubrique `storm-events`. 
    Vous devriez voir des journaux similaires aux journaux suivants :

    ```shell
    ....
    events-producer_1  | sent message to partition 0 offset 0
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 00:00:00.0000000,13208,NORTH CAROLINA,Thunderstorm Wind,Public
    events-producer_1  | 
    events-producer_1  | sent message to partition 0 offset 1
    events-producer_1  | event  2007-01-01 00:00:00.0000000,2007-01-01 05:00:00.0000000,23358,WISCONSIN,Winter Storm,COOP Observer
    ....
    ```
    
1. Pour consulter les journaux, exécutez la commande suivante dans un terminal distinct :

    ```shell
    docker-compose logs -f | grep kusto-connect
    ```
    
### <a name="start-the-connector"></a>Démarrer le connecteur

Utilisez un appel REST Kafka Connect pour démarrer le connecteur.

1. Dans un autre terminal, lancez la tâche du récepteur avec la commande suivante :

    ```shell
    curl -X POST -H "Content-Type: application/json" --data @adx-sink-config.json http://localhost:8083/connectors
    ```
    
1. Pour vérifier l’état, exécutez la commande suivante dans un autre terminal :
    
    ```shell
    curl http://localhost:8083/connectors/storm/status
    ```

Le connecteur démarre la mise en file d’attente des processus d’ingestion dans Azure Data Explorer.

> [!NOTE]
> Si vous rencontrez des problèmes de connecteur de journaux, [signalez-les](https://github.com/Azure/kafka-sink-azure-kusto/issues).

## <a name="query-and-review-data"></a>Interroger et examiner les données

### <a name="confirm-data-ingestion"></a>Confirmer l’ingestion de données

1. Attendez que les données arrivent dans la table `Storms`. Pour confirmer le transfert de données, vérifiez le nombre de lignes :
    
    ```kusto
    Storms | count
    ```

1. Confirmez qu’il n’y a pas de défaillance dans le processus d’ingestion :

    ```kusto
    .show ingestion failures
    ```
    
    Une fois que vous voyez les données, essayez quelques requêtes. 

### <a name="query-the-data"></a>Interroger les données

1. Pour voir tous les enregistrements, exécutez la [requête](write-queries.md) suivante :
    
    ```kusto
    Storms
    ```

1. Utilisez `where` et `project` pour filtrer des données spécifiques :
    
    ```kusto
    Storms
    | where EventType == 'Drought' and State == 'TEXAS'
    | project StartTime, EndTime, Source, EventId
    ```
    
1. Utilisez l’opérateur [`summarize`](https://docs.microsoft.com/azure/data-explorer/write-queries#summarize) :

    ```kusto
    Storms
    | summarize event_count=count() by State
    | where event_count > 10
    | project State, event_count
    | render columnchart
    ```
    
    :::image type="content" source="media/ingest-data-kafka/kusto-query.png" alt-text="Créer une table dans le portail Azure Data Explorer ":::

Pour plus de conseils et d’exemples de requête, consultez [Écrire des requêtes pour Azure Data Explorer](write-queries.md) et la [Documentation sur le langage de requête Kusto](https://docs.microsoft.com/azure/data-explorer/kusto/query/).

## <a name="reset"></a>Réinitialiser

Pour réinitialiser, procédez comme suit :

1. Arrêter les conteneurs (`docker-compose down -v`)
1. Supprimer (`drop table Storms`)
1. Recréer la table `Storms`
1. Recréer le mappage de table
1. Redémarrer les conteneurs (`docker-compose up`)

## <a name="clean-up-resources"></a>Nettoyer les ressources

Pour supprimer les ressources Azure Data Explorer, utilisez [az cluster delete](https://docs.microsoft.com/cli/azure/kusto/cluster#az-kusto-cluster-delete) ou [az Kusto database delete](https://docs.microsoft.com/cli/azure/kusto/database#az-kusto-database-delete) :

```azurecli-interactive
az kusto cluster delete -n <cluster name> -g <resource group name>
az kusto database delete -n <database name> --cluster-name <cluster name> -g <resource group name>
```

## <a name="next-steps"></a>Étapes suivantes

* Explorez en détail l’[architecture de big data](/azure/architecture/solution-ideas/articles/big-data-azure-data-explorer).
* Apprenez à [Ingérer des exemples de données au format JSON dans Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/ingest-json-formats?tabs=kusto-query-language).
* Pour d’autres labos Kafka :
   * [Hands on lab for ingestion from Confluent Cloud Kafka in distributed mode](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/confluent-cloud/README.md)
   * [Hands on lab for ingestion from HDInsight Kafka in distributed mode](https://github.com/Azure/azure-kusto-labs/tree/master/kafka-integration/distributed-mode/hdinsight-kafka)
   * [Hands on lab for ingestion from Confluent IaaS Kafka on AKS in distributed mode](https://github.com/Azure/azure-kusto-labs/blob/master/kafka-integration/distributed-mode/confluent-kafka/README.md)
