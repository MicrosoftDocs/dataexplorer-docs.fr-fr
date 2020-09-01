---
title: Ingérer des données de Kafka dans Azure Data Explorer
description: Dans cet article, vous allez apprendre à ingérer (charger) des données dans Azure Data Explorer depuis Kafka.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: how-to
ms.date: 06/03/2019
ms.openlocfilehash: 64b36db01bc1ab9867025723bf966279b4b4b115
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874815"
---
# <a name="ingest-data-from-kafka-into-azure-data-explorer"></a>Ingérer des données de Kafka dans Azure Data Explorer
 
L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. Azure Data Explorer permet l’ingestion (le chargement de données) à partir de Kafka. Kafka est une plateforme de streaming distribuée qui permet la création de pipelines de données de streaming en temps réel, qui déplacent les données de façon fiable entre des systèmes ou des applications.
 
## <a name="prerequisites"></a>Conditions préalables requises
 
* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer. 
 
* [Un cluster et une base de données de test](create-cluster-database-portal.md).
 
* [Un exemple d’application](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/kafka) qui génère des données et les envoie à Kafka.

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) pour exécuter l’exemple d’application.
 
## <a name="kafka-connector-setup"></a>Installation du connecteur Kafka

Kafka Connect est un outil pour le streaming de données scalable et fiable entre Apache Kafka et d’autres systèmes. Il permet de définir simplement et rapidement des connecteurs qui déplacent des grandes collections de données vers et depuis Kafka. ADX Kafka Sink sert de connecteur depuis Kafka.
 
### <a name="bundle"></a>Bundle

Kafka peut charger un `.jar` en tant que plug-in qui fera office de connecteur personnalisé. Pour produire un tel `.jar`, nous allons cloner le code localement et générer une build avec Maven. 

#### <a name="clone"></a>Clone

```bash
git clone git://github.com:Azure/kafka-sink-azure-kusto.git
cd ./kafka-sink-azure-kusto/kafka/
```

#### <a name="build"></a>Build

Générez une build localement avec Maven pour produire un `.jar` complet avec des dépendances.

* [Téléchargement](https://www.oracle.com/technetwork/java/javase/downloads/index.html) de JDK >= 1.8
* [Téléchargement](https://maven.apache.org/install.html) de Maven
 

Dans le répertoire racine *kafka-sink-azure-kusto*, exécutez :

```bash
mvn clean compile assembly:single
```

### <a name="deploy"></a>Déployer 

Chargez le plug-in dans Kafka. Vous trouverez un exemple de déploiement avec docker dans [kafka-sink-azure-kusto](https://github.com/Azure/kafka-sink-azure-kusto#deploy)
 

Vous trouverez une documentation sur les connecteurs Kafka et sur la façon de les déployer dans [Kafka Connect](https://kafka.apache.org/documentation/#connect) 

### <a name="example-configuration"></a>Exemple de configuration 
 
```config
name=KustoSinkConnector 
connector.class=com.microsoft.azure.kusto.kafka.connect.sink.KustoSinkConnector 
kusto.sink.flush_interval_ms=300000 
key.converter=org.apache.kafka.connect.storage.StringConverter 
value.converter=org.apache.kafka.connect.storage.StringConverter 
tasks.max=1 
topics=testing1 
kusto.tables.topics_mapping=[{'topic': 'testing1','db': 'daniel', 'table': 'TestTable','format': 'json', 'mapping':'TestMapping'}] 
kusto.auth.authority=XXX 
kusto.url=https://ingest-{mycluster}.kusto.windows.net/ 
kusto.auth.appid=XXX 
kusto.auth.appkey=XXX 
kusto.sink.tempdir=/var/tmp/ 
kusto.sink.flush_size=1000
```
 
## <a name="create-a-target-table-in-adx"></a>Créer une table cible dans ADX
 
Créez une table dans ADX à laquelle Kafka peut envoyer des données. Créez la table dans le cluster et la base de données provisionnés dans les **prérequis**.
 
1. Dans le portail Azure, accédez à votre cluster, puis sélectionnez **Requête**.
 
    ![Lien d’application de requête](media/ingest-data-event-hub/query-explorer-link.png)
 
1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter**.
 
    ```Kusto
    .create table TestTable (TimeStamp: datetime, Name: string, Metric: int, Source:string)
    ```
 
    ![Exécuter la requête de création](media/ingest-data-event-hub/run-create-query.png)
 
1. Copiez la commande suivante dans la fenêtre et sélectionnez **Exécuter**.
 
    ```Kusto
    .create table TestTable ingestion json mapping 'TestMapping' '[{"column":"TimeStamp","path":"$.timeStamp","datatype":"datetime"},{"column":"Name","path":"$.name","datatype":"string"},{"column":"Metric","path":"$.metric","datatype":"int"},{"column":"Source","path":"$.source","datatype":"string"}]'
    ```

    Cette commande mappe les données JSON entrantes aux noms de colonne et aux types de données de la table (TestTable).


## <a name="generate-sample-data"></a>Générer un exemple de données

Maintenant que le cluster Kafka est connecté à ADX, utilisez l’[exemple d’application](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que vous avez téléchargé pour générer des données.

### <a name="clone"></a>Clone

Clonez l’exemple d’application localement :

```cmd
git clone git://github.com:Azure/azure-kusto-samples-dotnet.git
cd ./azure-kusto-samples-dotnet/kafka/
```

### <a name="run-the-app"></a>Exécuter l’application

1. Ouvrez la solution de l’application exemple dans Visual Studio.

1. Dans le fichier `Program.cs`, mettez à jour la constante `connectionString` avec votre chaîne de connexion Kafka.

    ```csharp    
    const string connectionString = @"<YourConnectionString>";
    ```

1. Générez et exécutez l'application. L’application envoie des messages au cluster Kafka et affiche son état toutes les 10 secondes.

1. Une fois que l’application a envoyé quelques messages, passez à l’étape suivante.
 
## <a name="query-and-review-the-data"></a>Interroger et passer en revue les données

1. Pour vérifier qu’aucune erreur ne s’est produite au cours de l’ingestion :

    ```Kusto
    .show ingestion failures
    ```

1. Pour voir les données nouvellement ingérées :

    ```Kusto
    TestTable 
    | count
    ```

1. Pour voir le contenu des messages :
 
    ```Kusto
    TestTable
    ```
 
    Le jeu de résultats doit ressembler à ce qui suit :
 
    ![Jeu de résultats des messages](media/ingest-data-event-hub/message-result-set.png)
 
## <a name="next-steps"></a>Étapes suivantes
 
* [Interroger des données dans Azure Data Explorer](web-query-data.md)
