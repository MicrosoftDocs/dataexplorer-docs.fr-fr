---
title: Ingérer des données avec le kit SDK .NET Standard Azure Data Explorer (préversion)
description: Dans cet article, vous découvrez comment ingérer (charger) des données dans Azure Data Explorer avec le kit SDK .NET Standard.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: e24ebc2b89c7890f8818b0491c0cd6b8fbfd85fe
ms.sourcegitcommit: ee90472a4f9d751d4049744d30e5082029c1b8fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83722148"
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
        CslCommandGenerator.GenerateTableMappingCreateCommand(
            Data.Ingestion.IngestionMappingKind.Csv,
            table,
            tableMapping,
            new[] {
                new ColumnMapping() { ColumnName = "StartTime", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "0" } } },
                new ColumnMapping() { ColumnName = "EndTime", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "1" } } },
                new ColumnMapping() { ColumnName = "EpisodeId", Properties = new Dictionary<string, string>() { { MappingConsts.Ordinal, "2" } } },
                new ColumnMapping() { ColumnName = "EventId", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "3" } } },
                new ColumnMapping() { ColumnName = "State", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "4" } } },
                new ColumnMapping() { ColumnName = "EventType", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "5" } } },
                new ColumnMapping() { ColumnName = "InjuriesDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "6" } } },
                new ColumnMapping() { ColumnName = "InjuriesIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "7" } } },
                new ColumnMapping() { ColumnName = "DeathsDirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "8" } } },
                new ColumnMapping() { ColumnName = "DeathsIndirect", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "9" } } },
                new ColumnMapping() { ColumnName = "DamageProperty", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "10" } } },
                new ColumnMapping() { ColumnName = "DamageCrops", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "11" } } },
                new ColumnMapping() { ColumnName = "Source", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "12" } } },
                new ColumnMapping() { ColumnName = "BeginLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "13" } } },
                new ColumnMapping() { ColumnName = "EndLocation", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "14" } } },
                new ColumnMapping() { ColumnName = "BeginLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "15" } } },
                new ColumnMapping() { ColumnName = "BeginLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "16" } } },
                new ColumnMapping() { ColumnName = "EndLat", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "17" } } },
                new ColumnMapping() { ColumnName = "EndLon", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "18" } } },
                new ColumnMapping() { ColumnName = "EpisodeNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "19" } } },
                new ColumnMapping() { ColumnName = "EventNarrative", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "20" } } },
                new ColumnMapping() { ColumnName = "StormSummary", Properties =  new Dictionary<string, string>() { { MappingConsts.Ordinal, "21" } } }
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
            IngestionMapping = new IngestionMapping()
            { 
                IngestionMappingReference = tableMapping
            },
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromStorageAsync(blobPath ingestionProperties: properties);
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
