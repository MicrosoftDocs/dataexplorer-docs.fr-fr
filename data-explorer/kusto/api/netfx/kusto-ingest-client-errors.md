---
title: Kusto. ingestion des erreurs & les exceptions-Azure Explorateur de données
description: Cet article décrit Kusto. deréception-Errors and exceptions in Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: a4da1c35d34a1677cc0608fdf70dcbdb5a3c7c73
ms.sourcegitcommit: c6cb2b1071048daa872e2fe5a1ac7024762c180e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96774603"
---
# <a name="kustoingest-errors-and-exceptions"></a>Erreurs et exceptions Kusto. deréception
Toute erreur au cours de la gestion de la réception côté client est indiquée par une exception C#.

## <a name="failures"></a>Échecs

### <a name="kustodirectingestclient-exceptions"></a>Exceptions KustoDirectIngestClient

Lors d’une tentative de réception à partir de plusieurs sources, des erreurs peuvent se produire pendant le processus d’ingestion. Si une ingestion échoue pour l’une des sources, elle est journalisée et le client continue d’ingérer les sources restantes. Une fois que vous avez effectué toutes les sources d’ingestion, une `IngestClientAggregateException` est levée et contient le `IList<IngestClientException> IngestionErrors` membre.

`IngestClientException` et ses classes dérivées contiennent un champ `IngestionSource` et un `Error` champ. Les deux champs créent ensemble un mappage, de la source dont l’ingestion a échoué, à l’erreur qui s’est produite lors de la tentative d’ingestion. Les informations peuvent être utilisées dans la `IngestionErrors` liste pour déterminer quelles sources n’ont pas pu être ingérées et pourquoi. L' `IngestClientAggregateException` exception contient également une propriété booléenne `GlobalError` qui indique si une erreur s’est produite pour toutes les sources.

### <a name="failures-ingesting-from-files-or-blobs"></a>Échecs de réception de fichiers ou d’objets BLOB

Si un échec d’ingestion se produit lors d’une tentative de réception à partir d’un objet BLOB ou d’un fichier, les sources d’ingestion ne sont pas supprimées, même si l' `deleteSourceOnSuccess` indicateur a la valeur `true` . Les sources sont conservées pour une analyse plus poussée. Une fois que l’origine de l’erreur est comprise, et si l’erreur ne provient pas de la source d’ingestion elle-même, l’utilisateur du client peut tenter de la rerecevoir.

### <a name="failures-ingesting-from-idatareader"></a>Échecs de réception à partir de IDataReader

Lors de la réception à partir de DataReader, les données à ingérer sont enregistrées dans un dossier temporaire dont l’emplacement par défaut est `<Temp Path>\Ingestions_<current date and time>` . Ce dossier par défaut est toujours supprimé après une réception réussie.

Dans les `IngestFromDataReader` `IngestFromDataReaderAsync` méthodes et, l' `retainCsvOnFailure` indicateur, dont la valeur par défaut est `false` , détermine si les fichiers doivent être conservés après l’échec de l’ingestion. Si cet indicateur a la valeur `false` , les données qui échouent à l’ingestion ne sont pas conservées, ce qui complique la compréhension de la cause du problème.

## <a name="kustoqueuedingestclient-exceptions"></a>Exceptions KustoQueuedIngestClient

`KustoQueuedIngestClient` ingère les données en téléchargeant des messages dans une file d’attente Azure. Si une erreur se produit avant ou pendant le processus de mise en file d’attente, une `IngestClientAggregateException` est levée à la fin du processus. L’exception levée inclut une collection de `IngestClientException` , qui contient la source de chaque échec et n’a pas été publiée dans la file d’attente. L’erreur qui s’est produite lors de la tentative de publication du message est également levée.

### <a name="posting-to-queue-failures-with-a-file-or-blob-as-a-source"></a>Échec de la publication dans une file d’attente avec un fichier ou un objet BLOB en tant que source

Si une erreur se produit lors de l’utilisation des `KustoQueuedIngestClient` `IngestFromFile/IngestFromBlob` méthodes de, les sources ne sont pas supprimées, même si l' `deleteSourceOnSuccess` indicateur a la valeur `true` . Au lieu de cela, les sources sont conservées pour une analyse plus poussée. 

Une fois que l’origine de l’erreur est comprise, et si l’erreur ne provient pas de la source d’ingestion elle-même, l’utilisateur du client peut tenter de refaire la file d’attente des données à l’aide des `IngestFromFile/IngestFromBlob` méthodes pertinentes avec la source en échec. 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>Échec de la publication dans la file d’attente avec IDataReader comme source

Lors de l’utilisation d’une source DataReader, les données à poster dans la file d’attente sont enregistrées dans un dossier temporaire dont l’emplacement par défaut est `<Temp Path>\Ingestions_<current date and time>` . Ce dossier est toujours supprimé une fois que les données ont été correctement publiées dans la file d’attente.
Dans les `IngestFromDataReader` `IngestFromDataReaderAsync` méthodes et, l' `retainCsvOnFailure` indicateur, dont la valeur par défaut est `false` , détermine si les fichiers doivent être conservés après l’échec de l’ingestion. Si cet indicateur a la valeur `false` , les données qui échouent à l’ingestion ne sont pas conservées, ce qui complique la compréhension de la cause du problème.

### <a name="common-failures"></a>Échecs courants
|Error                         |Motif           |Limitation des risques                                   |
|------------------------------|-----------------|---------------------------------------------|
|Le nom de la base de données <database name> n’existe pas| La base de données n’existe pas|Vérifiez le nom de la base de données dans `kustoIngestionProperties` /Create la base de données |
|Le nom de table de l’entité qui n’existe pas’table’n’a pas été trouvé.|La table n’existe pas et il n’existe aucun mappage de fichier CSV.| Ajouter le mappage CSV/créer la table requise |
|Objet BLOB <blob path> exclu pour la raison : le modèle JSON doit être géré avec le paramètre jsonMapping| Ingestion JSON quand aucun mappage JSON n’est fourni.|Fournir un mappage JSON |
|Échec du téléchargement de l’objet BLOB : « le serveur distant a retourné une erreur : (404) introuvable. »| Le blob n’existe pas.|Vérifiez que l’objet BLOB existe. S’il existe, réessayez et contactez l’équipe Kusto |
|Le mappage de colonnes JSON n’est pas valide : au moins deux éléments de mappage pointent vers la même colonne.| Le mappage JSON a 2 colonnes avec des chemins d’accès différents|Corriger le mappage JSON |
|EngineError-[UtilsException] `IngestionDownloader.Download` : un ou plusieurs fichiers n’ont pas pu être téléchargés (recherchez dans KustoLogs la valeur ActivityID : <GUID1> , RootActivityId : <GUID2> )| Échec du téléchargement d’un ou plusieurs fichiers. |Réessayer |
|Échec de l’analyse : le flux ayant l’ID' <stream name> 'a un format CSV incorrect, échec par stratégie ValidationOptions |Fichier CSV incorrect (par exemple, qui n’a pas le même nombre de colonnes sur chaque ligne). Échoue uniquement lorsque la stratégie de validation a la valeur `ValidationOptions` . ValidateCsvInputConstantColumns |Vérifiez vos fichiers CSV. Ce message s’applique uniquement aux fichiers CSV/TSV |
|`IngestClientAggregateException` avec le message d’erreur « paramètres obligatoires manquants pour la signature d’accès partagé valide » |La SAP utilisée est du service, et non du compte de stockage. |Utiliser la SAP du compte de stockage |

### <a name="ingestion-error-codes"></a>Codes d’erreur d’ingestion

Pour faciliter le traitement des échecs d’ingestion par programme, les informations d’échec sont enrichies avec un code d’erreur numérique ( `IngestionErrorCode enumeration` ).

Pour obtenir la liste complète des codes d’erreur de réception, consultez Codes d’erreur d’ingestion [dans Azure Explorateur de données](../../../error-codes.md).

## <a name="detailed-exceptions-reference"></a>Informations de référence sur les exceptions

### <a name="cloudqueuesnotfoundexception"></a>CloudQueuesNotFoundException

Déclenché quand aucune file d’attente n’a été retournée à partir du cluster Gestion des données

Classe de base : [exception](/dotnet/api/system.exception)

|Nom du champ |Type     |Signification
|-----------|---------|------------------------------|
|Error      | String  | Erreur qui s’est produite lors de la tentative de récupération des files d’attente à partir du DM
                            
S’applique uniquement lors de l’utilisation du [client de réception en file d’attente Kusto](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient).
Pendant le processus d’ingestion, plusieurs tentatives sont effectuées pour récupérer les files d’attente Azure liées au DM. Lorsque ces tentatives échouent, l’exception contenant la raison de l’échec est générée dans le champ « erreur ». Éventuellement, une exception interne dans le champ’InnerException’est également déclenchée.


### <a name="cloudblobcontainersnotfoundexception"></a>CloudBlobContainersNotFoundException

Levée quand aucun conteneur d’objets BLOB n’a été retourné à partir du cluster Gestion des données

Classe de base : [exception](/dotnet/api/system.exception)

|Nom du champ   |Type     |Signification       
|-------------|---------|------------------------------|
|KustoEndpoint| String  | Point de terminaison du DM pertinent
                            
S’applique uniquement lors de l’utilisation du [client de réception en file d’attente Kusto](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient).  
Lors de l’ingestion de sources qui ne se trouvent pas déjà dans un conteneur Azure, telles que des fichiers, DataReader ou Stream, les données sont chargées dans un objet BLOB temporaire en vue d’être ingérées. L’exception est levée lorsqu’aucun conteneur n’est trouvé pour le téléchargement des données.

### <a name="duplicateingestionpropertyexception"></a>DuplicateIngestionPropertyException

Déclenché quand une propriété d’ingestion est configurée plusieurs fois

Classe de base : [exception](/dotnet/api/system.exception)

|Nom du champ   |Type     |Signification       
|-------------|---------|------------------------------------|
|PropertyName | String  | Nom de la propriété dupliquée
                            
### <a name="postmessagetoqueuefailedexception"></a>PostMessageToQueueFailedException

Levée lors de l’échec de la publication d’un message dans la file d’attente

Classe de base : [exception](/dotnet/api/system.exception)

|Nom du champ   |Type     |Signification       
|-------------|---------|---------------------------------|
|QueueUri     | String  | URI de la file d’attente
|Error        | String  | Message d’erreur qui a été généré lors de la tentative de publication dans la file d’attente
                            
S’applique uniquement lors de l’utilisation du [client de réception en file d’attente Kusto](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient).  
Le client de réception mis en file d’attente ingère les données en téléchargeant un message dans la file d’attente Azure appropriée. En cas d’échec de publication, l’exception est levée. Elle contient l’URI de la file d’attente, la raison de l’échec dans le champ « erreur » et éventuellement une exception interne dans le champ « InnerException ».

### <a name="dataformatnotspecifiedexception"></a>DataFormatNotSpecifiedException

Déclenché lorsqu’un format de données est requis mais non spécifié dans `IngestionProperties`

Classe de base : IngestClientException

Lors de l’ingestion à partir d’un flux, un format de données doit être spécifié dans le [IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties)pour recevoir correctement les données. Cette exception est levée lorsque `IngestionProperties.Format` n’est pas spécifié.

### <a name="invaliduriingestclientexception"></a>InvalidUriIngestClientException

Déclenché lorsqu’un URI d’objet blob non valide est soumis en tant que source d’ingestion

Classe de base : IngestClientException

### <a name="compressfileingestclientexception"></a>CompressFileIngestClientException

Levée lorsque le client de réception ne parvient pas à compresser le fichier fourni pour l’ingestion

Classe de base : IngestClientException

Les fichiers sont compressés avant leur réception. L’exception est levée lors de l’échec d’une tentative de compression du fichier.

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

Levée lorsque le client de réception ne parvient pas à charger la source fournie pour l’ingestion vers un objet BLOB temporaire

Classe de base : IngestClientException

### <a name="sizelimitexceededingestclientexception"></a>SizeLimitExceededIngestClientException

Levée quand une source d’ingestion est trop grande

Classe de base : IngestClientException

|Nom du champ   |Type     |Signification       
|-------------|---------|-----------------------|
|Taille         | long    | Taille de la source d’ingestion
|MaxSize      | long    | Taille maximale autorisée pour l’ingestion

Si une source d’ingestion dépasse la taille maximale de 4 Go, l’exception est levée. La validation de la taille peut être remplacée par l' `IgnoreSizeLimit` indicateur dans la [classe IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties). Toutefois, il n’est pas recommandé d’ingérer des [sources uniques supérieures à 1 Go](about-kusto-ingest.md#ingestion-best-practices).

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException

Levée lorsque le client de réception ne parvient pas à charger le fichier fourni pour l’ingestion vers un objet BLOB temporaire

Classe de base : IngestClientException

### <a name="directingestclientexception"></a>DirectIngestClientException

Déclenché lorsqu’une erreur générale se produit lors d’une ingestion directe

Classe de base : IngestClientException

### <a name="queuedingestclientexception"></a>QueuedIngestClientException

Déclenché lorsqu’une erreur se produit pendant l’exécution d’une réception en file d’attente

Classe de base : IngestClientException

### <a name="ingestclientaggregateexception"></a>IngestClientAggregateException

Déclenché lorsqu’une ou plusieurs erreurs se produisent pendant une ingestion

Classe de base : [AggregateException](/dotnet/api/system.aggregateexception)

|Nom du champ      |Type                             |Signification       
|----------------|---------------------------------|-----------------------|
|IngestionErrors | IList<IngestClientException>    | Les erreurs qui se produisent lors d’une tentative de réception et les sources associées
|IsGlobalError   | bool                            | Indique si l’exception s’est produite pour toutes les sources


