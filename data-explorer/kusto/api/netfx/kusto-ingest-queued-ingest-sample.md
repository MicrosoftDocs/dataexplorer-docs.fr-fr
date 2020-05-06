---
title: Ingestion de données avec la bibliothèque Kusto. ingestion-Azure Explorateur de données
description: Cet article décrit l’ingestion des données avec la bibliothèque Kusto. ingestion d’Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/05/2020
ms.openlocfilehash: fe268d19e5f42308737b7c392c58c6c1dca071b3
ms.sourcegitcommit: 061eac135a123174c85fe1afca4d4208c044c678
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799609"
---
# <a name="data-ingestion-with-the-kustoingest-library"></a>Ingestion de données avec la bibliothèque Kusto. deréception

Cet article présente un exemple de code qui utilise la bibliothèque cliente Kusto. Desent pour l’ingestion de données. Le code détaille le mode recommandé d’ingestion pour les pipelines de niveau production, appelé ingestion en attente. Pour la bibliothèque Kusto. deréception, l’entité correspondante est l’interface [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) . Le code client interagit avec le service Azure Explorateur de données en publiant des notifications d’ingestion dans une file d’attente Azure. La référence à la file d’attente est obtenue à partir de l’entité Gestion des données responsable de l’ingestion. 

> [!NOTE]
> L’interaction avec le service de Gestion des données doit être authentifiée à l’aide de Azure Active Directory (Azure AD).

L’exemple utilise Azure AD l’authentification de l’utilisateur et s’exécute sous l’identité de l’utilisateur interactif.

## <a name="dependencies"></a>Dépendances

Cet exemple de code requiert les packages NuGet suivants :
* Microsoft. Kusto. deréception
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

Le code effectue les opérations suivantes.
1. Crée une table sur le `KustoLab` cluster Azure Explorateur de données partagé sous la `KustoIngestClientDemo` base de données
2. Configure un [objet de mappage de colonnes JSON](../../management/create-ingestion-mapping-command.md) sur cette table
3. Crée une instance [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) pour le `Ingest-KustoLab` service gestion des données
4. Configure [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) avec les options d’ingestion appropriées
5. Crée un MemoryStream rempli avec des données générées pour être ingérées
6. Ingère les données à l' `KustoQueuedIngestClient.IngestFromStream` aide de la méthode
7. Interroge les [Erreurs](kusto-ingest-client-status.md#tracking-ingestion-status-kustoqueuedingestclient) d’ingestion

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
