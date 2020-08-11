---
title: Ingérer des données à l'aide de la bibliothèque Node d'Azure Data Explorer
description: Dans cet article, vous apprendrez comment ingérer (charger) des données dans Azure Data Explorer à l’aide de Node.js.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 33117a41c48a6aa3a9fc33566f201e6009b288ac
ms.sourcegitcommit: bcd0c96b1581e43e33aa35f4d68af6dcb4979d39
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88039180"
---
# <a name="ingest-data-using-the-azure-data-explorer-node-library"></a>Ingérer des données à l'aide de la bibliothèque Node d'Azure Data Explorer

> [!div class="op_single_selector"]
> * [.NET](net-sdk-ingest-data.md)
> * [Python](python-ingest-data.md)
> * [Nœud](node-ingest-data.md)
> * [Go](go-ingest-data.md)

L’Explorateur de données Azure est un service d’exploration de données rapide et hautement évolutive pour les données des journaux et les données de télémétrie. L’Explorateur de données Azure fournit deux bibliothèques clientes pour Node : une [bibliothèque d’ingestion](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) et une [bibliothèque de données](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). Ces bibliothèques vous permettent d’ingérer (charger) des données dans un cluster et d’interroger les données de votre code. Dans cet article, vous allez d’abord créer une table et un mappage de données dans un cluster de test. Ensuite, vous allez mettre en file d’attente l’ingestion sur le cluster et valider les résultats.

Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Prérequis

En plus d’un abonnement Azure, vous devez disposer des éléments suivants pour suivre cet article :

* [Un cluster et une base de données de test](create-cluster-database-portal.md)

* [Node.js](https://nodejs.org/en/download/) installé sur votre ordinateur de développement

## <a name="install-the-data-and-ingest-libraries"></a>Installer les données et ingérer les bibliothèques

Installer *azure-kusto-ingest* et *azure-kusto-data*

```bash
npm i azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Ajouter les constantes et les instructions d’importation

Importer des classes à partir des bibliothèques

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
Pour authentifier une application, l’Explorateur de données Azure utilise votre ID de locataire Azure Active Directory. Pour connaître votre ID de locataire, suivez [Connaître votre ID de locataire Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).

Définissez les valeurs de `authorityId`, `kustoUri`, `kustoIngestUri` et `kustoDatabase` avant d’exécuter ce code.

```javascript
const cluster = "MyCluster";
const region = "westus";
const authorityId = "microsoft.com";
const kustoUri = `https://${cluster}.${region}.kusto.windows.net:443`;
const kustoIngestUri = `https://ingest-${cluster}.${region}.kusto.windows.net:443`;
const kustoDatabase  = "Weather";
```

Maintenant, créez la chaîne de connexion. Cet exemple utilise l’authentification de l’appareil pour accéder au cluster. Vous pouvez également utiliser un certificat d’application, une clé d’application, et un utilisateur et mot de passe Azure Active Directory.

Vous allez créer la table de destination et le mappage dans une étape ultérieure.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>Définir les informations du fichier source

Importez des classes supplémentaires et définissez des constantes pour le fichier de source de données. Cet exemple utilise un exemple de fichier hébergé sur Stockage Blob Azure. L’exemple de jeu de données **StormEvents** contient des données météorologiques des [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>Créer une table sur votre cluster de test

Créer une table qui correspond au schéma des données dans le fichier `StormEvents.csv`. Lorsque ce code s’exécute, il retourne un message similaire au suivant : *Pour vous connecter, utilisez un navigateur web pour ouvrir la page https://microsoft.com/devicelogin et entrez le code XXXXXXXXX pour l’authentification*. Suivez les étapes pour vous connecter, puis revenez pour exécuter le bloc de code suivant. Les blocs de code suivants qui établissent une connexion vous demanderont de vous reconnecter.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="define-ingestion-mapping"></a>Définir le mappage d’ingestion

Mappez les données CSV entrantes aux noms de colonne et aux types de données utilisés lors de la création de la table.

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="queue-a-message-for-ingestion"></a>Mettre en file d’attente un message pour l’ingestion

Mettez en file d’attente un message pour extraire les données du stockage d’objets blob et ingérer ces données dans l’Explorateur de données Azure.

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Vérifier que la table contient des données

Vérifier que les données ont été ingérées dans la table. Attendez cinq à dix minutes avant que l’ingestion en file d’attente planifie l’ingestion et charge les données dans l’Explorateur de données Azure. Exécutez ensuite le code suivant pour obtenir le nombre d’enregistrements de la table `StormEvents`.

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>Exécuter des requêtes de dépannage

Connectez-vous à [https://dataexplorer.azure.com](https://dataexplorer.azure.com) et à votre cluster. Exécutez la commande suivante dans votre base de données pour voir si des échecs d’ingestion se sont produits ces quatre dernières heures. Remplacez le nom de la base de données avant l’exécution.
    
```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Exécutez la commande suivante pour voir l’état de toutes les opérations d’ingestion des quatre dernières heures. Remplacez le nom de la base de données avant l’exécution.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez de suivre nos autres articles, conservez les ressources que vous avez créées. Dans le cas contraire, exécutez la commande suivante dans votre base de données pour nettoyer la table `StormEvents`.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Étapes suivantes

* [Écrire des requêtes](write-queries.md)
