---
title: Ingérer des données avec le kit SDK .NET Standard Azure Data Explorer (préversion)
description: Dans cet article, vous découvrez comment ingérer (charger) des données dans Azure Data Explorer avec le kit SDK .NET Standard.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 84eb95e818251e0812703700a8cfdd97f9b0561a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81494457"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Ingérer des données à l’aide du kit SDK .NET Standard Azure Data Explorer (préversion)

Azure Data Explorer (ADX) est un service d’exploration de données rapide et très scalable pour les données des journaux et de télémétrie. ADX fournit deux bibliothèques clientes pour .NET Standard : une [bibliothèque d’ingestion](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) et une [bibliothèque de données](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Ces bibliothèques vous permettent d’ingérer (charger) des données dans un cluster et d’interroger les données de votre code. Dans cet article, vous allez d’abord créer une table et un mappage de données dans un cluster de test. Ensuite, vous allez mettre en file d’attente l’ingestion sur le cluster et valider les résultats.

## <a name="prerequisites"></a>Conditions préalables requises

* Si vous n’avez pas d’abonnement Azure, créez un [compte Azure gratuit](https://azure.microsoft.com/free/) avant de commencer.

* [Un cluster et une base de données de test](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Installer la bibliothèque d’ingestion

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Authentication

Pour authentifier une application, l’Explorateur de données Azure utilise votre ID de locataire AAD. Pour trouver votre ID de locataire, utilisez l’URL suivante en remplaçant *YourDomain* par votre domaine.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Par exemple, si votre domaine est *contoso.com*, l’URL est : [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Cliquez sur cette URL pour voir les résultats. La première ligne est la suivante. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Ici, l’ID de locataire est `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

Cet exemple utilise un utilisateur et un mot de passe AAD pour l’authentification afin d’accéder au cluster. Vous pouvez également utiliser un certificat d’application AAD et une clé d’application AAD. Définissez vos valeurs pour `tenantId`, `user` et `password` avant d’exécuter ce code.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Construire la chaîne de connexion
Maintenant, créez la chaîne de connexion. Vous allez créer la table de destination et le mappage dans une étape ultérieure.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Définir les informations du fichier source

Définissez le chemin du fichier source. Cet exemple utilise un exemple de fichier hébergé sur Stockage Blob Azure. L’exemple de jeu de données **StormEvents** contient des données météorologiques des [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Créer une table sur votre cluster de test
Créez une table nommée `StormEvents` qui correspond au schéma des données dans le fichier `StormEvents.csv`.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("InjuriesIndirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Définir le mappage d’ingestion

Mappez les données CSV entrantes aux noms de colonnes utilisés lors de la création de la table.
Provisionner un [objet de mappage de colonne CSV](kusto/management/create-ingestion-mapping-command.md) sur cette table

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EpisodeId", Ordinal = 2 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "EventType", Ordinal = 5 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 15 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 21 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Mettre en file d’attente un message pour l’ingestion

Mettez en file d’attente un message pour extraire les données du stockage d’objets blob et ingérer ces données dans ADX.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Vérifier que les données ont été ingérées dans la table

Attendez cinq à dix minutes avant que l’ingestion en file d’attente planifie l’ingestion et charge les données dans ADX. Exécutez ensuite le code suivant pour obtenir le nombre d’enregistrements de la table `StormEvents`.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
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
