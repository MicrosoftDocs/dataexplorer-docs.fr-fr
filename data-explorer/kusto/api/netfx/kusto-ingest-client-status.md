---
title: Kusto.Ingest Reference - Ingestion Status Reporting - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto.Ingest Reference - Ingestion Status Reporting dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 1a3eed1db0ec7d3dd4bc83c0a65f342020b2a387
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523713"
---
# <a name="kustoingest-reference---ingestion-status-reporting"></a>Référence Kusto.Ingest - Rapport sur l’état de l’ingestion
Cet article explique comment utiliser les capacités [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) pour suivre l’état d’une demande d’ingestion.

## <a name="sourcedescription-datareaderdescription-streamdescription-filedescription-and-blobdescription"></a>SourceDescription, DataReaderDescription, StreamDescription, FileDescription, et BlobDescription
Ces différentes classes de description encapsulent des détails importants concernant les données sources à ingérer, et peuvent et même devraient être fournies à l’opération d’ingestion.
Toutes ces classes sont dérivées de la classe `SourceDescription` abstraite et sont utilisées pour instantanéifier un identifiant unique pour chaque source de données.
L’identifiant fourni est destiné au suivi ultérieur de l’état de l’opération et apparaît dans tous les rapports, traces et exceptions liés à l’opération appropriée.

### <a name="class-sourcedescription"></a>Source de classeDescription
>Lors de l’ingestion d’un grand jeu de données (par exemple, un DataReader de plus de 1 Go) - les données seront divisées en morceaux de 1 Go et ingérées séparément.<BR>Dans ce cas, la même SourceId s’appliquera à toutes les opérations d’ingérie provenant du même jeu de données.   

```csharp
public abstract class SourceDescription
{
    public Guid? SourceId { get; set; }
}
```

### <a name="class-datareaderdescription"></a>DataReaderDescription de classe
```csharp
public class DataReaderDescription : SourceDescription
{
    public  IDataReader DataReader { get; set; }
}
```

### <a name="class-streamdescription"></a>StreamDescription de classe
```csharp
public class StreamDescription : SourceDescription
{
    public Stream Stream { get; set; }
}
```

### <a name="class-filedescription"></a>Fichier de classeDescription
```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>Classe BlobDescription
```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>Représentation des résultats d’ingestion

### <a name="interface-ikustoingestionresult"></a>Interface IKustoIngestionResult
Cette interface capture le résultat d’une seule opération d’ingestion `SourceId`en file d’attente et permet sa récupération par .
```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>Ingestion de classeStatus
IngestionStatus encapsule un statut complet d’une seule opération d’ingestion.
```csharp
public class IngestionStatus
{
    // The ingestion status returns from the service. Status remains 'Pending' during the ingestion process and
    // is updated by the service once the ingestion completes. When <see cref="IngestionReportMethod"/> is set to 'Queue' the ingestion status
    // will always be 'Queued' and the caller needs to query the report queues for ingestion status, as configured. To query statuses that were
    // reported to queue, see: <see href="https://docs.microsoft.com/azure/kusto/api/netfx/kusto-ingest-client-status#ingestion-status-in-azure-queue"/>.
    // When <see cref="IngestionReportMethod"/> is set to 'Table', call <see cref="IKustoIngestionResult.GetIngestionStatusBySourceId"/> or
    // <see cref="IKustoIngestionResult.GetIngestionStatusCollection"/> to retrieve the most recent ingestion status.
    public Status Status { get; set; }
    // A unique identifier representing the ingested source. Can be supplied during the ingestion execution.
    public Guid IngestionSourceId { get; set; }
    // The URI of the blob, potentially including the secret needed to access the blob.
    // This can be a filesystem URI (on-premises deployments only), or an Azure Blob Storage URI (including a SAS key or a semicolon followed by the account key)
    public string IngestionSourcePath { get; set; }
    // The name of the database holding the target table.
    public string Database { get; set; }
    // The name of the target table into which the data will be ingested.
    public string Table { get; set; }
    // The last updated time of the ingestion status.
    public DateTime UpdatedOn { get; set; }
    // The ingestion's operation Id.
    public Guid OperationId { get; set; }
    // The ingestion operation activity Id.
    public Guid ActivityId { get; set; }
    // In case of a failure - indicates the failure's error code.
    public IngestionErrorCode ErrorCode { get; set; }
    // In case of a failure - indicates the failure's status.
    public FailureStatus FailureStatus { get; set; }
    // In case of a failure - indicates the failure's details.
    public string Details { get; set; }
    // In case of a failure - indicates whether or not the failures originate from an Update Policy.
    public bool OriginatesFromUpdatePolicy { get; set; }
}
```

### <a name="status-enumeration"></a>Énumération du statut
|Valeur |Signification |
|------------|------------|
|Pending |Temporaire. Pourrait changer au cours de l’ingestion en fonction de l’issue de l’opération d’ingestion |
|Opération réussie |Permanent. il données a été ingéré avec succès |
|Échec |Permanent. L’ingestion a échoué |
|Mis en file d'attente. |Permanent. Les données ont fait la queue pour ingestion |
|Ignoré |Permanent. Aucune donnée n’a été fournie et l’opération d’ingérie a été ignorée |
|Partiellement ensuccédé |Permanent. Une partie des données a été ingérée avec succès tandis que certaines |

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>Suivi du statut d’ingestion (KustoQueuedIngestClient)
[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) est un client « à l’origine du feu et de l’oubli » - l’opération d’ingestion du côté client se termine par l’affichage d’un message à une file d’attente Azure, après quoi le travail du client est fait. Pour la commodité de l’utilisateur client, KustoQueuedIngestClient fournit un mécanisme de suivi de l’état d’ingestion individuelle. Cela n’est pas destiné à l’utilisation de masse sur les pipelines d’ingestion à haut débit, mais plutôt pour l’ingestion «précision» lorsque le taux est relativement faible et les exigences de suivi sont très strictes.

> [!WARNING]
> Il faut éviter d’allumer des notifications positives pour chaque demande d’ingestion pour les flux de données à grand volume, car cela met une charge extrême sur les ressources sous-jacentes de xStore, > ce qui pourrait entraîner une latence accrue de l’ingestion et même une non-réactivité complète du cluster.


Les propriétés suivantes (définies sur [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)) contrôlent le niveau et le transport pour le succès d’ingestion ou les notifications d’échec :

### <a name="ingestionreportlevel-enumeration"></a>IngestionReportLevel énumération
```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>IngestionReportMethod énumération
```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

Pour être en mesure de suivre l’état de votre ingestion, assurez-vous de fournir ce qui suit à [l’IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) vous effectuez l’opération ingère avec:
1.  Définissez `IngestionReportLevel`la propriété au niveau de rapport souhaité - soit FailuresOnly (qui est la valeur par défaut) ou ÉchecsAndSuccesses.
Lorsqu’il est réglé à Aucun, rien ne sera signalé à la fin de l’ingestion.
2.  Spécifier le désiré `IngestionReportMethod` - File d’attente, table ou les deux.

Un exemple d’utilisation peut être trouvé sur la page [Kusto.Ingest Examples.](kusto-ingest-client-examples.md)

### <a name="ingestion-status-in-azure-table"></a>Statut d’ingestion dans la table Azure
L’interface `IKustoIngestionResult` qui est retournée de chaque opération ingérer contient des fonctions qui peuvent être utilisées pour interroger l’état de l’ingestion.
Portez une `Status` attention particulière `IngestionStatus` à la propriété des objets retournés :
* `Pending`indique que la source a fait la queue pour l’ingestion et n’a pas encore été mise à jour; utiliser à nouveau la fonction pour interroger l’état de la source
* `Succeeded`indique que la source a été ingérée avec succès
* `Failed`indique que la source n’a pas été ingérée

>Notez que `Queued` l’obtention `IngestionReportMethod` d’un statut indique que le a été laissé à sa valeur par défaut de «file d’attente». Il s’agit d’un statut permanent et la réinvocation de la GetIngestionStatusBySourceId ou GetIngestionStatusCollection fonctions se traduira toujours par le même statut «file d’attente».<BR>Pour pouvoir vérifier l’état d’une ingestion dans une table Azure, `IngestionReportMethod` veuillez vérifier avant d’ingestion que la propriété `Table` des `QueueAndTable` [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) est réglée (ou si vous souhaitez également que le statut d’ingestion soit signalé à une file d’attente).

### <a name="ingestion-status-in-azure-queue"></a>Statut d’ingestion dans la file d’attente Azure
Les `IKustoIngestionResult` méthodes ne sont pertinentes que pour vérifier un statut dans une table Azure. Pour interroger les statuts qui ont été signalés à une file d’attente Azure, utilisez les méthodes suivantes [d’IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient):

|Méthode |Objectif |
|------------|------------|
|PeekTopIngestionFailures (en) |Méthode Async qui renvoie des informations concernant les premières défaillances d’ingestion qui n’ont pas été écartées, selon la limite de messages demandés |
|GetAndDiscardTopIngestionFailures GetAndDiscardTopIngestionFailures GetAndDiscardTopIngestionFailures GetAnd |Méthode Async qui renvoie et rejette les premières défaillances d’ingestion qui n’ont pas été écartées, selon la limite de messages demandés |
|GetAndDiscardTopIngestionSuccesses |Méthode Async qui renvoie et rejette les premiers succès d’ingestion qui n’ont pas `IngestionReportLevel` été écartés, selon la limite de messages demandés (seulement pertinente si l’on le met en`FailuresAndSuccesses` |


### <a name="ingestion-failures-retrieved-from-azure-queue"></a>Échecs d’ingestion récupérés de la file d’attente Azure
Les défaillances d’ingestion `IngestionFailure` sont représentées par un objet qui contient des informations utiles concernant l’échec :

|Propriété |Signification |
|------------|------------|
|Table de & de base de données |La base de données et les noms de table prévus |
|IngestionSourcePath |Le chemin du blob ingéré. Will contient le nom de fichier original en cas d’ingestion du fichier. Sera aléatoire en cas d’ingestion de DataReader |
|ÉchecStatus |`Permanent`(aucune nouvelle tentative ne `Transient` sera exécutée), (la `Exhausted` nouvelle tentative sera exécutée) ou (plusieurs retries ont également échoué) |
|OperationId & RootActivityId |Opération ID et RootActivity ID de l’ingestion (utile pour d’autres dépannages) |
|ÉchecOn |TEMPS UTC de l’échec. Sera plus grand que le moment où la méthode d’ingestion a été appelée, car les données sont agrégées avant d’exécuter l’ingestion |
|Détails |Autres détails concernant l’échec (s’il y en a) |
|ErrorCode |`IngestionErrorCode`l’énumération, représentant le code d’erreur d’ingestion, au cas où l’échec|