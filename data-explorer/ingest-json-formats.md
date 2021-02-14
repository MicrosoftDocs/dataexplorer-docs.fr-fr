---
title: Ingérer des données au format JSON dans Azure Data Explorer
description: Découvrez comment ingérer des données au format JSON dans Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: how-to
ms.date: 05/19/2020
ms.openlocfilehash: 5c0de46e6b6b14be7076533204e63504368e71ad
ms.sourcegitcommit: d77e52909001f885d14c4d421098a2c492b8c8ac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772481"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Ingérer des exemples de données au format JSON dans Azure Data Explorer

Cet article vous montre comment ingérer des données au format JSON dans une base de données Azure Data Explorer. Vous commencerez par des exemples simples de données JSON brutes et mappées, puis vous passerez à des données JSON multilignes, et enfin à des schémas JSON plus complexes contenant des tableaux et des dictionnaires.  Les exemples décrivent en détail le processus d’ingestion de données au format JSON en utilisant le langage de requête Kusto (KQL), C# ou Python. Les commandes de contrôle `ingest` du langage de requête Kusto sont exécutées directement sur le point de terminaison du moteur. Dans les scénarios de production, l’ingestion est exécutée vers le service Gestion des données à l’aide de bibliothèques clientes ou de connexions de données. Lisez [Ingérer des données à l’aide de la bibliothèque Python d’Azure Data Explorer](python-ingest-data.md) et [Ingérer des données à l’aide du Kit de développement logiciel (SDK) .NET Standard d’Azure Data Explorer](./net-sdk-ingest-data.md) pour obtenir une procédure pas à pas concernant l’ingestion de données avec ces bibliothèques clientes.

## <a name="prerequisites"></a>Prérequis

[Un cluster et une base de données de test](create-cluster-database-portal.md)

## <a name="the-json-format"></a>Le format JSON

Azure Data Explorer prend en charge deux formats de fichier JSON :
* `json`: JSON séparé par une ligne. Chaque ligne des données d’entrée contient exactement un enregistrement JSON.
* `multijson`: JSON multiligne. L’analyseur ignore les séparateurs de ligne et lit un enregistrement de la position précédente jusqu’à la fin d’un JSON valide.

### <a name="ingest-and-map-json-formatted-data"></a>Ingérer et mapper des données au format JSON

L’ingestion de données au format JSON vous oblige à spécifier le *format* à l’aide de la [propriété d’ingestion](ingestion-properties.md). L’ingestion de données JSON requiert un [mappage](kusto/management/mappings.md), lequel mappe une entrée de source JSON à sa colonne cible. Lors d’une ingestion de données, utilisez la propriété `IngestionMapping` avec sa propriété d’ingestion `ingestionMappingReference` (pour un mappage prédéfini) ou sa propriété `IngestionMappings`. Cet article utilise la propriété d’ingestion `ingestionMappingReference`, qui est prédéfinie sur la table utilisée pour l’ingestion. Dans les exemples ci-dessous, nous allons commencer par ingérer des enregistrements JSON en tant que données brutes dans une table à une seule colonne. Nous utiliserons ensuite le mappage pour ingérer chaque propriété dans sa colonne mappée. 

### <a name="simple-json-example"></a>Exemple JSON simple

L’exemple suivant est un JSON simple, avec une structure plate. Les données comportent des informations sur la température et l’humidité, collectées par plusieurs appareils. Chaque enregistrement est marqué d’un ID et d’un timestamp.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Ingérer des enregistrements JSON bruts 

Dans cet exemple, vous ingérez des enregistrements JSON en tant que données brutes dans une table à une seule colonne. La manipulation des données, l’utilisation de requêtes et la stratégie de mise à jour sont effectuées une fois que les données sont ingérées.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Utilisez le langage de requête Kusto pour ingérer des données au format JSON brut.

1. Connectez-vous à [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Sélectionnez **Ajouter un cluster**.

1. Dans la boîte de dialogue **Ajouter un cluster**, entrez l’URL de votre cluster sous la forme `https://<ClusterName>.<Region>.kusto.windows.net/`, puis sélectionnez **Ajouter**.

1. Collez la commande suivante, puis sélectionnez **Exécuter** pour créer la table.

    ```kusto
    .create table RawEvents (Event: dynamic)
    ```

    Cette requête crée la table munie d’une seule colonne `Event` d’un type de données[dynamique](kusto/query/scalar-data-types/dynamic.md).

1. Créez le mappage JSON.

    ```kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","Properties":{"path":"$"}}]'
    ```

    Cette commande crée un mappage et mappe le chemin d’accès racine JSON `$` à la colonne `Event`.

1. Ingérez des données dans la table `RawEvents`.

    ```kusto
    .ingest into table RawEvents ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json') with '{"format":"json", "ingestionMappingReference":"DiagnosticRawRecordsMapping"}'
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

Utilisez C# pour ingérer des données au format JSON brut.

1. Créez la table `RawEvents`.

    ```csharp
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Créez le mappage JSON.
    
    ```csharp
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Json,
            tableName,
            tableMapping,
            new[] {
            new ColumnMapping {ColumnName = "Events", Properties = new Dictionary<string, string>() {
                {"path","$"} }
            } });

    kustoClient.ExecuteControlCommand(command);
    ```
    Cette commande crée un mappage et mappe le chemin d’accès racine JSON `$` à la colonne `Event`.

1. Ingérez des données dans la table `RawEvents`.

    ```csharp
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json"; 
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };

    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```

> [!NOTE]
> Les données sont agrégées conformément à la [stratégie de traitement par lot](kusto/management/batchingpolicy.md), ce qui entraîne une latence de quelques minutes.

# <a name="python"></a>[Python](#tab/python)

Utilisez Python pour ingérer des données au format JSON brut.

1. Créez la table `RawEvents`.

    ```python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Créez le mappage JSON.

    ```python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingérez des données dans la table `RawEvents`.

    ```python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Les données sont agrégées conformément à la [stratégie de traitement par lot](kusto/management/batchingpolicy.md), ce qui entraîne une latence de quelques minutes.

---

## <a name="ingest-mapped-json-records"></a>Ingérer des enregistrements JSON mappés

Dans cet exemple, vous ingérez des données d’enregistrements JSON. Chaque propriété JSON est mappée à une colonne unique de la table. 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Créez une nouvelle table, avec un schéma similaire aux données d’entrée JSON. Nous utiliserons cette table pour tous les exemples et commandes d’ingestion suivants. 

    ```kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Créez le mappage JSON.

    ```kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","Properties":{"path":"$.timestamp"}},{"column":"Device","Properties":{"path":"$.deviceId"}},{"column":"MessageId","Properties":{"path":"$.messageId"}},{"column":"Temperature","Properties":{"path":"$.temperature"}},{"column":"Humidity","Properties":{"path":"$.humidity"}}]'
    ```

    Dans ce mappage, comme défini par le schéma de la table, les entrées `timestamp` sont ingérées dans la colonne `Time` en tant que types de données `datetime`.

1. Ingérez des données dans la table `Events`.

    ```kusto
    .ingest into table Events ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json') with '{"format":"json", "ingestionMappingReference":"FlatEventMapping"}'
    ```

    Le fichier « simple.json » comporte quelques enregistrements JSON séparés par des lignes. Le format est `json`, et le mappage utilisé dans la commande d’ingestion est le `FlatEventMapping` que vous avez créé.

# <a name="c"></a>[C#](#tab/c-sharp)

1. Créez une nouvelle table, avec un schéma similaire aux données d’entrée JSON. Nous utiliserons cette table pour tous les exemples et commandes d’ingestion suivants. 

    ```csharp
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Créez le mappage JSON.

    ```csharp
    var tableMapping = "FlatEventMapping";
    var command =
         CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Json,
            "",
            tableMapping,
            new[]
            {
               new ColumnMapping() {ColumnName = "Time", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.timestamp"} } },
               new ColumnMapping() {ColumnName = "Device", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.deviceId" } } },
               new ColumnMapping() {ColumnName = "MessageId", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.messageId" } } },
               new ColumnMapping() {ColumnName = "Temperature", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.temperature" } } },
               new ColumnMapping() { ColumnName= "Humidity", Properties = new Dictionary<string, string>() {{ MappingConsts.Path, "$.humidity" } } },
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    Dans ce mappage, comme défini par le schéma de la table, les entrées `timestamp` sont ingérées dans la colonne `Time` en tant que types de données `datetime`.    

1. Ingérez des données dans la table `Events`.

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };

    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```

    Le fichier « simple.json » comporte quelques enregistrements JSON séparés par des lignes. Le format est `json`, et le mappage utilisé dans la commande d’ingestion est le `FlatEventMapping` que vous avez créé.

# <a name="python"></a>[Python](#tab/python)

1. Créez une nouvelle table, avec un schéma similaire aux données d’entrée JSON. Nous utiliserons cette table pour tous les exemples et commandes d’ingestion suivants. 

    ```python
    TABLE = "Events"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Créez le mappage JSON.

    ```python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","Properties":{"path":"$.timestamp"}},{"column":"Device","Properties":{"path":"$.deviceId"}},{"column":"MessageId","Properties":{"path":"$.messageId"}},{"column":"Temperature","Properties":{"path":"$.temperature"}},{"column":"Humidity","Properties":{"path":"$.humidity"}}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingérez des données dans la table `Events`.

    ```python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Le fichier « simple.json » comporte quelques enregistrements JSON séparés par des lignes. Le format est `json`, et le mappage utilisé dans la commande d’ingestion est le `FlatEventMapping` que vous avez créé.    
---

## <a name="ingest-multi-lined-json-records"></a>Ingérer des enregistrements JSON multilignes

Dans cet exemple, vous ingérez des enregistrements JSON multilignes. Chaque propriété JSON est mappée à une colonne unique de la table. Le fichier « multilined.json » comporte quelques enregistrements JSON mis en retrait. Le format `multijson` indique au moteur de lire les enregistrements par la structure JSON.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Ingérez des données dans la table `Events`.

```kusto
.ingest into table Events ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json') with '{"format":"multijson", "ingestionMappingReference":"FlatEventMapping"}'
```

# <a name="c"></a>[C#](#tab/c-sharp)

Ingérez des données dans la table `Events`.

```csharp
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMapping = new IngestionMapping()
        {
            IngestionMappingReference = tableMapping
        }
    };

ingestClient.IngestFromStorageAsync(blobPath, properties);
```

# <a name="python"></a>[Python](#tab/python)

Ingérez des données dans la table `Events`.

```python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Ingérer des enregistrements JSON contenant des tableaux

Les données de type tableau sont des collections ordonnées de valeurs. L’ingestion d’un tableau JSON est effectuée par une [stratégie de mise à jour](kusto/management/update-policy.md). Le JSON est ingéré tel quel dans une table intermédiaire. Une stratégie de mise à jour exécute une fonction prédéfinie sur la table `RawEvents`, en ingérant de nouveau les résultats dans la table cible. Nous ingérerons les données avec la structure suivante :

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Créez une fonction `update policy` qui développe la collection de `records` pour que chaque valeur de la collection reçoive une ligne distincte, à l’aide de l’opérateur `mv-expand`. Nous utiliserons la table `RawEvents` en tant que table source et `Events` comme table cible.

    ```kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. Le schéma reçu par la fonction doit correspondre au schéma de la table cible. Utilisez l’opérateur `getschema` pour examiner le schéma.

    ```kusto
    EventRecordsExpand() | getschema
    ```

1. Ajoutez la stratégie de mise à jour à la table cible. Cette stratégie exécute automatiquement la requête sur toutes les nouvelles données ingérées dans la table intermédiaire `RawEvents` et ingère ses résultats dans la table `Events`. Définissez une stratégie de rétention zéro pour éviter la persistance de la table intermédiaire.

    ```kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Ingérez des données dans la table `RawEvents`.

    ```kusto
    .ingest into table RawEvents ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json') with '{"format":"multijson", "ingestionMappingReference":"RawEventMapping"}'
    ```

1. Examinez les données dans la table `Events`.

    ```kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Créez une fonction de mise à jour qui développe la collection de `records` pour que chaque valeur de la collection reçoive une ligne distincte, à l’aide de l’opérateur `mv-expand`. Nous utiliserons la table `RawEvents` en tant que table source et `Events` comme table cible.   

    ```csharp
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > Le schéma reçu par la fonction doit correspondre au schéma de la table cible.

1. Ajoutez la stratégie de mise à jour à la table cible. Cette stratégie exécute automatiquement la requête sur toutes les nouvelles données ingérées dans la table intermédiaire `RawEvents` et ingère ses résultats dans la table `Events`. Définissez une stratégie de rétention zéro pour éviter la persistance de la table intermédiaire.

    ```csharp
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingérez des données dans la table `RawEvents`.

    ```csharp
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };

    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```
    
1. Examinez les données dans la table `Events`.

# <a name="python"></a>[Python](#tab/python)

1. Créez une fonction de mise à jour qui développe la collection de `records` pour que chaque valeur de la collection reçoive une ligne distincte, à l’aide de l’opérateur `mv-expand`. Nous utiliserons la table `RawEvents` en tant que table source et `Events` comme table cible.   

    ```python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > Le schéma reçu par la fonction doit correspondre au schéma de la table cible.

1. Ajoutez la stratégie de mise à jour à la table cible. Cette stratégie exécute automatiquement la requête sur toutes les nouvelles données ingérées dans la table intermédiaire `RawEvents` et ingère ses résultats dans la table `Events`. Définissez une stratégie de rétention zéro pour éviter la persistance de la table intermédiaire.

    ```python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingérez des données dans la table `RawEvents`.

    ```python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Examinez les données dans la table `Events`.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Ingérer des enregistrements JSON contenant des dictionnaires

Un JSON structuré dans un dictionnaire contient des paires clé-valeur. Les enregistrements JSON sont soumis à un mappage d’ingestion à l’aide d’une expression logique dans le `JsonPath`. Vous pouvez ingérer des données avec la structure suivante :

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Créez un mappage JSON.

    ```kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"a","Properties":{"path":"$.event[?(@.Key == \'timestamp\')]"}},{"column":"b","Properties":{"path":"$.event[?(@.Key == \'deviceId\')]"}},{"column":"c","Properties":{"path":"$.event[?(@.Key == \'messageId\')]"}},{"column":"d","Properties":{"path":"$.event[?(@.Key == \'temperature\')]"}},{"column":"Humidity","datatype":"string","Properties":{"path":"$.event[?(@.Key == \'humidity\')]"}}]'
    ```

1. Ingérez des données dans la table `Events`.

    ```kusto
    .ingest into table Events ('https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json') with '{"format":"multijson", "ingestionMappingReference":"KeyValueEventMapping"}'
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Créez un mappage JSON.

    ```csharp
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
         CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Json,
            "",
            tableMapping,
            new[]
            {
                new ColumnMapping() { ColumnName = "Time", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'timestamp')]"
                } } },
                    new ColumnMapping() { ColumnName = "Device", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'deviceId')]"
                } } }, new ColumnMapping() { ColumnName = "MessageId", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'messageId')]"
                } } }, new ColumnMapping() { ColumnName = "Temperature", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'temperature')]"
                } } }, new ColumnMapping() { ColumnName = "Humidity", Properties = new Dictionary<string, string>() { {
                    MappingConsts.Path,
                    "$.event[?(@.Key == 'humidity')]"
                } } },
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingérez des données dans la table `Events`.

    ```csharp
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMapping = new IngestionMapping()
            {
                IngestionMappingReference = tableMapping
            }
        };
    ingestClient.IngestFromStorageAsync(blobPath, properties);
    ```

# <a name="python"></a>[Python](#tab/python)

1. Créez un mappage JSON.

    ```python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingérez des données dans la table `Events`.

     ```python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Étapes suivantes

* [Vue d’ensemble de l’ingestion des données](ingest-data-overview.md)
* [Écrire des requêtes](write-queries.md)
