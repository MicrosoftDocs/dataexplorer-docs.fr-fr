---
title: Déplacez les données entre les clusters Azure Data Explorer et Spark au moyen du connecteur Azure Data Explorer pour Apache Spark.
description: Cette rubrique vous montre comment déplacer des données entre des clusters Azure Data Explorer et Apache Spark.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: 0c8d1add5f5f72ad96475df67ec2ff20bc9364c0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81492389"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Connecteur Azure Data Explorer pour Apache Spark

[Apache Spark](https://spark.apache.org/) est un moteur d’analytique unifié pour le traitement des données à grande échelle. Azure Data Explorer est un service d’analytique données rapide et entièrement managé dédié à l’analyse en temps réel de volumes importants de données de streaming. 

Le connecteur Azure Data Explorer pour Spark est un [projet open source](https://github.com/Azure/azure-kusto-spark) qui peut s’exécuter sur n’importe quel cluster Spark. Il implémente la source de données et le récepteur de données pour déplacer les données entre les clusters Azure Data Explorer et Spark. À l’aide d’Azure Data Explorer et d’Apache Spark, vous pouvez rapidement créer des applications scalables ciblant des scénarios basés sur les données. Par exemple, les scénarios de machine learning (ML), les scénarios ETL et les scénarios Log Analytics. Avec le connecteur, Azure Data Explorer devient un magasin de données valide pour les opérations de source et de réception Spark standard, telles que write, read et writeStream.

Vous pouvez écrire des données dans Azure Data Explorer en mode batch ou streaming. La fonctionnalité de lecture dans Azure Data Explorer prend en charge le nettoyage des colonnes et le pushdown des prédicats, ce qui filtre les données d’Azure Data Explorer et réduit le volume de données transférées.

Cette rubrique explique comment installer et configurer le connecteur Azure Data Explorer pour Spark, et comment déplacer des données entre Azure Data Explorer et les clusters Apache Spark.

> [!NOTE]
> Bien que certains des exemples ci-dessous fassent référence à un cluster Spark [Azure Databricks](https://docs.azuredatabricks.net/), le connecteur Spark Azure Data Explorer ne dépend pas directement de ce cluster, ni d’aucune autre distribution Spark.

## <a name="prerequisites"></a>Prérequis

* [Créer un cluster et une base de données Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) 
* Créer un cluster Spark
* Installez la bibliothèque du connecteur Azure Data Explorer :
    * Bibliothèques prédéfinies pour [Spark 2.4 et Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Dépôt Maven](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* Installation de [Maven 3.x](https://maven.apache.org/download.cgi)

> [!TIP]
> Les versions 2.3.x sont également prises en charge, mais il peut s’avérer nécessaire de modifier certaines dépendances du fichier pom.xml.

## <a name="how-to-build-the-spark-connector"></a>Comment créer un connecteur Spark

> [!NOTE]
> Cette étape est facultative. Si vous utilisez des bibliothèques prédéfinies, accédez à la section [Configuration du cluster Spark](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Configuration requise

1. Installez les bibliothèques listées dans [Dependencies](https://github.com/Azure/azure-kusto-spark#dependencies), notamment les bibliothèques suivantes du [SDK Kusto Java ](kusto/api/java/kusto-java-client-library.md) :
    * [Client de données Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Client d’ingestion Kusto](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Reportez-vous à [cette source](https://github.com/Azure/azure-kusto-spark) pour la création du connecteur Spark.

1. Pour les applications Scala/Java utilisant des définitions de projet Maven, liez votre application à l’artefact suivant (la dernière version peut être différente) :
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Commandes de génération

Pour générer un fichier jar et exécuter tous les tests :

```
mvn clean package
```

Pour générer un fichier jar, exécutez tous les tests et installez le fichier jar dans votre référentiel Maven local :

```
mvn clean install
```

Pour en savoir plus, consultez la section relative à [l’utilisation des connecteurs](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Configuration du cluster Spark

> [!NOTE]
> Il est recommandé d’utiliser la dernière version du connecteur Spark Azure Data Explorer lorsque vous effectuez les étapes suivantes :

1. Configurez les paramètres suivants du cluster Spark en fonction du cluster Azure Databricks, en utilisant Spark 2.4.4 et Scala 2.11 :

    ![Paramètres de cluster Databricks](media/spark-connector/databricks-cluster.png)
    
1. Installez la dernière bibliothèque spark-kusto-connector à partir de Maven :
    
    ![Importer des bibliothèques](media/spark-connector/db-libraries-view.png) ![Sélectionner Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Vérifiez que toutes les bibliothèques requises sont installées :

    ![Vérification de l’installation des bibliothèques](media/spark-connector/db-libraries-view.png)

1. Si vous souhaitez effectuer une installation à l’aide d’un fichier JAR, vérifiez que des dépendances supplémentaires ont été installées :

    ![Ajout de dépendances](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Authentification

Le connecteur Azure Data Explorer pour Spark vous permet de vous authentifier auprès d’Azure Active Directory (Azure AD) à l’aide de l’une des méthodes suivantes :
* Avec une [application Azure AD](#azure-ad-application-authentication)
* Avec un [jeton d’accès Azure AD](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* Avec l’[authentification de l’appareil](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (pour les scénarios autres que les scénarios de production)
* Un coffre de clés [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) Pour accéder à la ressource Key Vault, installez le package azure-keyvault et entrez les informations d’identification de l’application.

### <a name="azure-ad-application-authentication"></a>Authentification de l’application Azure AD

L’authentification de l’application Azure AD est la méthode d’authentification la plus simple et la plus courante qui est recommandée pour le connecteur Azure Data Explorer pour Spark.

|Propriétés  |Description  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   ID client d’application (client) Azure AD.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Autorité d’authentification Azure AD. ID Azure AD Directory (locataire).        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Clé d’application Azure AD pour le client.     |

### <a name="azure-data-explorer-privileges"></a>Privilèges Azure Data Explorer

Accordez les privilèges suivants sur un cluster Azure Data Explorer :

* Pour la lecture (source de données), l’identité Azure AD doit disposer de privilèges de *lecteur* pour la base de données cible ou de privilèges d’*administrateur* pour la table cible.
* Pour l’écriture (récepteur de données), l’identité Azure AD doit bénéficier de privilèges d’*ingesteur* pour la base de données cible. Elle doit également bénéficier de privilèges *d’utilisateur* sur la base de données cible pour pouvoir créer des tables. Si la table cible existe déjà, vous devez configurer des privilèges d’*administrateur* pour celle-ci.
 
Pour en savoir plus sur les principaux rôles Azure Data Explorer, consultez la section relative à [l’autorisation basée sur les rôles](kusto/management/access-control/role-based-authorization.md). Pour savoir comment gérer les rôles de sécurité, consultez la section relative à la [gestion des rôles de sécurité](kusto/management/security-roles.md).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Récepteur Spark : écriture de données dans Azure Data Explorer

1. Configurez les paramètres du récepteur :

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Écrivez la trame de données Spark dans le cluster Azure Data Explorer en tant que lot :

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Ou utilisez la syntaxe simplifiée :
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Écrivez les données de diffusion en continu :

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Source Spark : lecture des données depuis Azure Data Explorer

1. Lors de la lecture de [petites quantités de données](kusto/concepts/querylimits.md), définissez la requête de données :

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Facultatif : Si c’est **vous** qui fournissez le stockage des objets blob temporaires (et non Azure Data Explorer), les objets blob seront créés sous la responsabilité de l’appelant. Cela comprend le provisionnement du stockage, la rotation des clés d’accès et la suppression des artefacts temporaires. 
    Le module KustoBlobStorageUtils contient des fonctions d’assistance permettant de supprimer des objets blob en fonction des coordonnées du compte et du conteneur, et des informations d’identification du compte, ou en fonction d’une URL SAS complète disposant d’autorisations d’écriture, de lecture et de liste. Lorsque le jeu de données RDD correspondant n’est plus nécessaire, chaque transaction stocke les artefacts d’objets blob temporaires dans un répertoire distinct. Ce répertoire est capturé dans les journaux des transactions de lecture signalés sur le nœud du pilote Spark.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    Dans l’exemple ci-dessus, le coffre de clés n’est pas accessible à l’aide de l’interface du connecteur. Pour l’utilisation des secrets Databricks, une méthode plus simple est utilisée.

1. Lisez les données dans Azure Data Explorer.

    * Si c’est **vous** qui fournissez le stockage des objets blob temporaires, lisez les données à partir d’Azure Data Explorer de la façon suivante :

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Si c’est **Azure Data Explorer** qui fournit le stockage des objets blob temporaires, lisez les données à partir d’Azure Data Explorer de la façon suivante :
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur le [connecteur Spark Azure Data Explorer](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Exemple de code pour Java et Python](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
