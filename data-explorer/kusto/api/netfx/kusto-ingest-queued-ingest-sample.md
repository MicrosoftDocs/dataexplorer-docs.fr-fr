---
title: HowTo Data Ingestion avec Kusto.Ingest Library - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit HowTo Data Ingestion avec Kusto.Ingest Library in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: 80b2b61c70269c5bd166a064fe9d0e2c59dd8197
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523628"
---
# <a name="howto-data-ingestion-with-kustoingest-library"></a>HowTo Data Ingestion avec la bibliothèque Kusto.Ingest
Cet article présente un exemple de code qui utilise la bibliothèque cliente Kusto.Ingest.

## <a name="overview"></a>Vue d’ensemble
L’échantillon de code suivant démontre l’ingestion de données de Queued (via le service de gestion de données Kusto) à Kusto avec l’utilisation de la bibliothèque Kusto.Ingest.

> Cet article traite du mode recommandé d’ingestion pour les pipelines de qualité production, qui est également appelé **Ingestion en file d’attente** (en termes de la bibliothèque Kusto.Ingest, l’entité correspondante est [l’interface IKustoQueuedIngestClient).](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) Dans ce mode, le code client interagit avec le service Kusto en affichant des messages de notification d’ingestion à une file d’attente Azure, référence à laquelle est obtenu de la gestion des données Kusto (alias. Service d’ingestion). L’interaction avec le service de gestion des données doit être authentifiée avec **AAD**.

#### <a name="authentication"></a>Authentification
Cet exemple de code utilise l’authentification de l’utilisateur AAD et s’exécute sous l’identité de l’utilisateur interactif.

## <a name="dependencies"></a>Les dépendances
Ce code d’échantillon nécessite les paquets NuGet suivants :
* Microsoft.Kusto.Ingest (en)
* Microsoft.IdentityModel.Clients.ActiveDirectory
* WindowsAzure.Storage
* Newtonsoft.Json

## <a name="namespaces-used"></a>Espaces de noms utilisés
```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading;
using Kusto.Data;
using Kusto.Data.Common;
using Kusto.Data.Net.Client;
using Kusto.Ingest;
```

## <a name="code"></a>Code
Le code présenté ci-dessous effectue ce qui suit :
1. Crée un `KustoLab` tableau sur le `KustoIngestClientDemo` cluster Kusto partagé sous la base de données
2. Dispositions d’un [objet de cartographie de colonne JSON](../../management/create-ingestion-mapping-command.md) sur cette table
3. Crée un [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) `Ingest-KustoLab` pour le service de gestion des données
4. Met en place [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) avec des options d’ingestion appropriées
5. Crée un MemoryStream rempli de certaines données générées à ingérer
6. Ingests les `KustoQueuedIngestClient.IngestFromStream` données à l’aide de la méthode
7. Sondages pour toute [erreur d’ingestion](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient)

```csharp
static void Main(string[] args)
{
    var clusterName = "KustoLab";
    var db = "KustoIngestClientDemo";
    var table = "Table1";
    var mappingName = "Table1_mapping_1";
    var serviceNameAndRegion = "clusterNameAndRegion"; // For example, "mycluster.westus"
    var authority = "AAD Tenant or name"; // For example, "microsoft.com"

    // Set up table
    var kcsbEngine =
        new KustoConnectionStringBuilder($"https://{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var kustoAdminClient = KustoClientFactory.CreateCslAdminProvider(kcsbEngine))
    {
        var columns = new List<Tuple<string, string>>()
        {
            new Tuple<string, string>("Column1", "System.Int64"),
            new Tuple<string, string>("Column2", "System.DateTime"),
            new Tuple<string, string>("Column3", "System.String"),
        };

        var command = CslCommandGenerator.GenerateTableCreateCommand(table, columns);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);

        // Set up mapping
        var columnMappings = new List<JsonColumnMapping>();
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column1", JsonPath = "$.Id" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column2", JsonPath = "$.Timestamp" });
        columnMappings.Add(new JsonColumnMapping()
            { ColumnName = "Column3", JsonPath = "$.Message" });

        command = CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
                                            table, mappingName, columnMappings);
        kustoAdminClient.ExecuteControlCommand(databaseName: db, command: command);
    }

    // Create Ingest Client
    var kcsbDM =
        new KustoConnectionStringBuilder($"https://ingest-{serviceNameAndRegion}.kusto.windows.net").WithAadUserPromptAuthentication(authority: $"{authority}");

    using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(kcsbDM))
    {
        var ingestProps = new KustoQueuedIngestionProperties(db, table);
        // For the sake of getting both failure and success notifications we set this to IngestionReportLevel.FailuresAndSuccesses
        // Usually the recommended level is IngestionReportLevel.FailuresOnly
        ingestProps.ReportLevel = IngestionReportLevel.FailuresAndSuccesses;
        ingestProps.ReportMethod = IngestionReportMethod.Queue;
        ingestProps.JSONMappingReference = mappingName;
        ingestProps.Format = DataSourceFormat.json;

        // Prepare data for ingestion
        using (var memStream = new MemoryStream())
        using (var writer = new StreamWriter(memStream))
        {
            for (int counter = 1; counter <= 10; ++counter)
            {
                writer.WriteLine(
                    "{{ \"Id\":\"{0}\", \"Timestamp\":\"{1}\", \"Message\":\"{2}\" }}",
                    counter, DateTime.UtcNow.AddSeconds(100 * counter),
                    $"This is a dummy message number {counter}");
            }

            writer.Flush();
            memStream.Seek(0, SeekOrigin.Begin);

            // Post ingestion message
            ingestClient.IngestFromStream(memStream, ingestProps, leaveOpen: true);
        }

        // Wait and retrieve all notifications
        //  - Actual duration should be decided based on the effective Ingestion Batching Policy set on the table/database
        Thread.Sleep(<timespan>);
        var errors = ingestClient.GetAndDiscardTopIngestionFailures().GetAwaiter().GetResult();
        var successes = ingestClient.GetAndDiscardTopIngestionSuccesses().GetAwaiter().GetResult();

        errors.ForEach((f) => { Console.WriteLine($"Ingestion error: {f.Info.Details}"); });
        successes.ForEach((s) => { Console.WriteLine($"Ingested: {s.Info.IngestionSourcePath}"); });
    }
}
```