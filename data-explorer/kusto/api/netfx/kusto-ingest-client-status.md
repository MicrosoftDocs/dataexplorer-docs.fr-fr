---
title: Rapport d’état d’ingestion Kusto. ingestion-Azure Explorateur de données
description: Cet article décrit la création de rapports d’état de l’ingestion Kusto. ingestion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: 76ae07e2e7bdbb15900385b1e2feab0c9ff97d01
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373628"
---
# <a name="kustoingest-ingestion-status-reporting"></a>Rapport d’état d’ingestion Kusto. ingestion

Cet article explique comment utiliser les fonctionnalités de [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) pour effectuer le suivi de l’état d’une demande d’ingestion.

## <a name="description-classes"></a>Classes de description

Ces classes de description contiennent des informations importantes sur les données sources à ingérer et doivent être utilisées dans l’opération d’ingestion. 

* SourceDescription
* DataReaderDescription
* StreamDescription
* FileDescription
* BlobDescription

Les classes sont toutes dérivées de la classe abstraite `SourceDescription` , et sont utilisées pour instancier un identificateur unique pour chaque source de données. Chaque identificateur est ensuite utilisé pour le suivi de l’État et s’affiche dans tous les rapports, traces et exceptions liés à l’opération concernée.

### <a name="class-sourcedescription"></a>SourceDescription de classe

Les jeux de données volumineux seront répartis dans des blocs de 1 Go, et chaque partie sera ingérée séparément. La même SourceId s’appliquera alors à toutes les opérations de réception provenant du même DataSet.   

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

### <a name="class-filedescription"></a>FileDescription de classe

```csharp
public class FileDescription : SourceDescription
{
    public string FilePath { get; set; }
}
```

### <a name="class-blobdescription"></a>BlobDescription de classe

```csharp
public class BlobDescription : SourceDescription
{
    public string BlobUri { get; set; }
    // Setting the Blob size here is important, as this saves the client the trouble of probing the blob for size
    public long? BlobSize { get; set; }
}
```

## <a name="ingestion-result-representation"></a>Représentation du résultat d’ingestion

### <a name="interface-ikustoingestionresult"></a>Interface IKustoIngestionResult

Cette interface capture le résultat d’une opération de réception en file d’attente unique et peut être récupérée par `SourceId` .

```csharp
public interface IKustoIngestionResult
{
    // Retrieves the detailed ingestion status of the ingestion source with the given sourceId.
    IngestionStatus GetIngestionStatusBySourceId(Guid sourceId);

    // Retrieves the detailed ingestion status of all data ingestion operations into Kusto associated with this IKustoIngestionResult instance.
    IEnumerable<IngestionStatus> GetIngestionStatusCollection();
}
```

### <a name="class-ingestionstatus"></a>IngestionStatus de classe

IngestionStatus contient l’état complet d’une opération d’ingestion unique.

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

### <a name="status-enumeration"></a>Énumération d’État

|Valeur              |Signification                                                                                     |Temporaire/permanent
|-------------------|-----------------------------------------------------------------------------------------------------|---------|
|Pending            |La valeur peut changer au cours de l’ingestion, en fonction du résultat de l’opération d’ingestion |Temporaire|
|Opération réussie          |Les données ont été ingérées avec succès                                                              |Permanent| 
|Échec             |Échec de l’ingestion                                                                                     |Permanent|
|Mis en file d'attente.             |Les données ont été mises en file d’attente pour être ingérées                                                               |Permanent|
|Ignoré            |Aucune donnée n’a été fournie et l’opération de réception a été ignorée                                            |Permanent|
|PartiallySucceeded |Une partie des données a été correctement ingérée, alors que d’autres ont échoué                                        |Permanent|

## <a name="tracking-ingestion-status-kustoqueuedingestclient"></a>État de l’ingestion du suivi (KustoQueuedIngestClient)

[IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) est un client « Fire-and-oublier ». L’opération d’ingestion côté client se termine par la publication d’un message dans une file d’attente Azure. Après la publication, le travail du client est effectué. Pour la commodité de l’utilisateur client, KustoQueuedIngestClient fournit un mécanisme permettant de suivre l’état de l’ingestion individuelle. Ce mécanisme n’est pas destiné à une utilisation massive sur des pipelines d’ingestion à débit élevé. Ce mécanisme est destiné à l’ingestion de la précision lorsque la vitesse est relativement faible et que les exigences de suivi sont strictes.

> [!WARNING]
> L’activation des notifications positives pour chaque demande d’ingestion des flux de données à volume élevé doit être évitée, car cela place une charge extrême sur les ressources xStore sous-jacentes, ce qui peut entraîner une latence accrue de l’ingestion et même une non-réactivité du cluster.

Les propriétés suivantes (définies sur [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties)) contrôlent le niveau et le transport des notifications de réussite ou d’échec de l’ingestion.

### <a name="ingestionreportlevel-enumeration"></a>Énumération IngestionReportLevel

```csharp
public enum IngestionReportLevel
{
    FailuresOnly = 0,
    None,
    FailuresAndSuccesses
}
```

### <a name="ingestionreportmethod-enumeration"></a>Énumération IngestionReportMethod

```csharp
public enum IngestionReportMethod
{
    Queue = 0,
    Table,
    QueueAndTable
}
```

Pour suivre l’état de votre réception, fournissez les informations suivantes au [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) avec lequel vous effectuez l’opération d’ingestion :
1.  Définissez `IngestionReportLevel` la propriété sur le niveau de rapport requis. Soit `FailuresOnly` (valeur par défaut), soit `FailuresAndSuccesses` .
Quand la valeur `None` est, rien n’est signalé à la fin de l’ingestion.
1.  Spécifiez `IngestionReportMethod`  -  `Queue` , `Table` ou `both` .

Vous trouverez un exemple d’utilisation sur la page d' [exemples Kusto. deréception](kusto-ingest-client-examples.md) .

### <a name="ingestion-status-in-the-azure-table"></a>État de l’ingestion dans le tableau Azure

L' `IKustoIngestionResult` interface retournée à partir de chaque opération de réception contient des fonctions qui peuvent être utilisées pour interroger l’état de l’ingestion.
Portez une attention particulière à la `Status` propriété des objets retournés `IngestionStatus` :
* `Pending`indique que la source a été mise en file d’attente pour réception et qu’elle doit être mise à jour. Réutiliser la fonction pour interroger l’état de la source
* `Succeeded`indique que la source a été incorrectement gérée
* `Failed`indique que la source n’a pas pu être gérée

> [!NOTE]
> L’obtention d’un `Queued` État indique que la `IngestionReportMethod` valeur par défaut de « queue » a été conservée. Il s’agit d’un état permanent et de la révocation des `GetIngestionStatusBySourceId` `GetIngestionStatusCollection` fonctions ou, aura toujours pour résultat le même État « en file d’attente ».
> Pour vérifier l’état d’une ingestion dans une table Azure, avant de l’ingérer, vérifiez que la `IngestionReportMethod` propriété de [KustoQueuedIngestionProperties](kusto-ingest-client-reference.md#class-kustoqueuedingestionproperties) a la valeur `Table` . Si vous souhaitez également que l’état d’ingestion soit signalé dans une file d’attente, définissez l’État sur `QueueAndTable` .

### <a name="ingestion-status-in-azure-queue"></a>État de l’ingestion dans la file d’attente Azure

Les `IKustoIngestionResult` méthodes ne sont pertinentes que pour la vérification d’un État dans une table Azure. Pour interroger les États qui ont été signalés dans une file d’attente Azure, utilisez les méthodes [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) suivantes.

|Méthode                                  |Objectif     |
|----------------------------------------|------------|
|PeekTopIngestionFailures                |Méthode Async qui retourne des informations sur les échecs de réception les plus anciens qui n’ont pas encore été ignorés en raison de la limite des messages demandés |
|GetAndDiscardTopIngestionFailures       |Méthode Async qui retourne et ignore les échecs de réception les plus anciens qui n’ont pas encore été ignorés en raison de la limite des messages demandés |
|GetAndDiscardTopIngestionSuccesses      |Méthode Async qui retourne et ignore les réussites de réception les plus anciennes qui n’ont pas encore été ignorées en raison de la limite des messages demandés. Cette méthode est uniquement pertinente si `IngestionReportLevel` a la valeur.`FailuresAndSuccesses` |

### <a name="ingestion-failures-retrieved-from-the-azure-queue"></a>Échecs d’ingestion récupérés à partir de la file d’attente Azure

Les échecs d’ingestion sont représentés par l' `IngestionFailure` objet qui contient des informations utiles sur l’échec.

|Propriété                      |Signification     |
|------------------------------|------------|
|Table de & de base de données              |Nom de la base de données et de la table prévus |
|IngestionSourcePath           |Chemin d’accès de l’objet BLOB ingéré. Contient le nom de fichier d’origine si le fichier est ingéré. Sera aléatoire si DataReader est ingéré |
|FailureStatus                 |`Permanent`(aucune nouvelle tentative ne sera exécutée), `Transient` (nouvelle tentative sera exécutée) ou `Exhausted` (plusieurs nouvelles tentatives ont également échoué) |
|OperationId & RootActivityId  |ID d’opération et ID de l’activité rootActivity de l’ingestion (utile pour un dépannage supplémentaire) |
|FailedOn                      |Heure UTC de l’échec. Est supérieur au moment où la méthode d’ingestion a été appelée, car les données sont agrégées avant l’exécution de l’ingestion |
|Détails                       |Autres détails concernant l’échec (le cas échéant) |
|ErrorCode                     |`IngestionErrorCode`énumération, représente le code d’erreur d’ingestion, en cas d’échec. |
