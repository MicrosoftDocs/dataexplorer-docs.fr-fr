---
title: Kusto.Ingest Reference - Erreurs et exceptions - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Kusto.Ingest Reference - Erreurs et Exceptions dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: f8f50322a79dea8890b4a4ad5eaa78f0b8fe3bc0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81524342"
---
# <a name="kustoingest-reference---errors-and-exceptions"></a>Kusto.Ingest Référence - Erreurs et exceptions
Toute erreur lors de la manipulation d’ingestion du côté du client est exposée au code utilisateur via une exception C.

## <a name="failures-overview"></a>Aperçu des échecs

### <a name="kustodirectingestclient-exceptions"></a>KustoDirectIngestClient Exceptions
Tout en essayant d’ingérer à partir de sources multiples, des erreurs peuvent se produire lors de l’ingestion de certaines de ces sources, tandis que d’autres peuvent être ingérées avec succès. Si une ingestion échoue pour une source particulière, elle est enregistrée et le client continue d’ingérer les sources restantes pour l’ingestion. Après avoir dépassé toutes les sources `IngestClientAggregateException` d’ingestion, un est jeté, contenant un membre `IList<IngestClientException> IngestionErrors`.
`IngestClientException`et ses classes dérivées contiennent un champ `IngestionSource` et un `Error` champ qui, ensemble, forment une cartographie à partir de la source qui n’a pas été ingérée à l’erreur qui s’est produite en tentant de l’ingérer. On peut utiliser les informations dans la liste IngestionErrors pour enquêter sur les sources qui n’ont pas été ingérées et pourquoi. `IngestClientAggregateException`exception contient également une `GlobalError`propriété boolean , qui indique si une erreur s’est produite pour toutes les sources.

### <a name="failures-ingesting-from-files-or-blobs"></a>Échecs ingestion de fichiers ou blobs 
Si une défaillance d’ingestion s’est produite en essayant d’ingérer à partir `deleteSourceOnSuccess` du fichier `true`blob, les sources d’ingestion ne sont pas supprimées, même si le drapeau est réglé à .
Les sources sont conservées pour une analyse plus approfondie. Une fois comprendre l’origine de l’erreur et étant donné que l’erreur n’a pas provenu de la source d’ingestion elle-même, l’utilisateur du client peut tenter de la réingérer.

### <a name="failures-ingesting-from-idatareader"></a>Échecs ingérant d’IDataReader
Lors de l’ingestion de DataReader, les données à l’ingérer sont enregistrées dans un dossier temporaire dont l’emplacement par défaut est `<Temp Path>\Ingestions_<current date and time>`. Ce dossier est toujours supprimé après une ingestion réussie.<BR>
Dans `IngestFromDataReader` le `IngestFromDataReaderAsync` et `retainCsvOnFailure` les méthodes, le `false`drapeau, dont la valeur par défaut est , détermine si les fichiers doivent être conservés après une ingestion a échoué. Si ce drapeau `false`est réglé à , les données qui échouent l’ingestion ne serait pas persisté, ce qui rend difficile de comprendre ce qui s’est mal passé.

## <a name="kustoqueuedingestclient-exceptions"></a>KustoQueuedIngestClient Exceptions
KustoQueuedIngestClient ingère des données en téléchargeant des messages dans une file d’attente Azure. Si une erreur se produit avant et `IngestClientAggregateException` pendant le processus de file d’attente, un est jeté à la fin de l’exécution avec une collection de qui contient la source qui n’a pas été affiché à la file d’attente (pour chaque échec) et l’erreur qui s’est produite lors de la tentative de `IngestClientException` poster le message.

### <a name="posting-to-queue-failures-with-file-or-blob-as-a-source"></a>Affichage aux défaillances de file d’attente avec le fichier ou Blob comme source
Si une erreur s’est produite lors de l’utilisation de KustoQueuedIngestClient IngestFromFile/ IngestFromBlob méthodes, les sources ne sont pas supprimés, même si le `deleteSourceOnSuccess` drapeau est réglé à `true`, mais sont plutôt conservés pour une analyse plus approfondie. Une fois comprendre l’origine de l’erreur et étant donné que l’erreur n’est pas originaire de la source elle-même, l’utilisateur du client peut tenter de re-filer des données en utilisant les méthodes pertinentes IngestFromFile/IngestFromBlob avec la source défaillante. 

### <a name="posting-to-queue-failures-with-idatareader-as-a-source"></a>Affichage aux échecs de file d’attente avec IDataReader comme source
Lors de l’utilisation d’une source DataReader, les données à poster `<Temp Path>\Ingestions_<current date and time>`à la file d’attente est enregistrée à un dossier temporaire dont l’emplacement par défaut est .
Ce dossier est toujours supprimé après que les données ont été affichées avec succès dans la file d’attente.
Dans `IngestFromDataReader` le `IngestFromDataReaderAsync` et `retainCsvOnFailure` les méthodes, le `false`drapeau, dont la valeur par défaut est , détermine si les fichiers doivent être conservés après une ingestion a échoué. Si ce drapeau `false`est réglé à , les données qui échouent l’ingestion ne serait pas persisté, ce qui rend difficile de comprendre ce qui s’est mal passé.

### <a name="common-failures"></a>Échecs courants
|Error|Motif|Limitation des risques|
|------------------------------|----|------------|
|Nom <database name> de base de données n’existe pas| La base de données n’existe pas|Consultez le nom de la base de données à kustoIngestionProperties/Créer la base de données |
|Le nom de table de l’entité qui n’existe pas » de type « Table » n’a pas été trouvé.|La table n’existe pas et il n’y a pas de cartographie CSV.| Ajouter la cartographie CSV / créer la table requise |
|Blob <blob path> exclu pour cause : le modèle de json doit être ingéré avec le paramètre de jsonMapping| Json ingestion quand aucune cartographie de json fourni.|Fournir une cartographie JSON |
|Échec à télécharger blob: «Le serveur distant retourné une erreur: (404) Non trouvé.| Le blob n’existe pas.|Vérifier l’objet blob existe, s’il existe une nouvelle tentative et contactez l’équipe Kusto |
|La cartographie de colonne de Json n’est pas valide : deux éléments de cartographie ou plus indiquent la même colonne.| La cartographie JSON a 2 colonnes avec des chemins différents|Correction de la cartographie JSON |
|EngineError - [UtilsException] IngestionDownloader.Download: Un ou plusieurs fichiers n’ont pas<GUID1>été téléchargés<GUID2>(recherche KustoLogs pour ActivityID: , RootActivityId: )| Un ou plusieurs fichiers n’ont pas été téléchargés. |Recommencer |
|Échec à analyser: Stream with<stream name>id ' ' a un format Csv mal formé, échouant par validationOptions politique |Fichier csv malformé (p. ex., pas le même nombre de colonnes sur chaque ligne). Échec seulement lorsque la stratégie de validation est définie à ValidationOptions. ValidateCsvInputConstantColumns |Vérifiez vos fichiers csv. Ce message ne s’applique que sur les fichiers csv/tsv |
|IngestClientAggregateException avec un message d’erreur 'Missing mandatory parameters for valid Shared Access Signature' |Le SAS utilisé est du service, et non du compte de stockage |Utilisez le SAS du compte de stockage |

### <a name="ingestion-error-codes"></a>Codes d’erreur d’ingestion

Pour aider à gérer les défaillances d’ingestion de façon programmatique, les informations d’échec sont enrichies d’un code d’erreur numérique (ingestionError Recensement du code).

|ErrorCode|Motif|
|-----------|-------|
|Unknown| Une erreur inconnue s'est produite|
|Stream_LowMemoryCondition| L’opération a manqué de mémoire|
|Stream_WrongNumberOfFields| Le document CSV a un nombre incohérent de champs|
|Stream_InputStreamTooLarge| Le document soumis à l’ingestion a dépassé la taille autorisée|
|Stream_NoDataToIngest| Trouvé aucun flux de données à ingérer|
|Stream_DynamicPropertyBagTooLarge| L’une des colonnes dynamiques des données ingérées contient trop de propriétés uniques|
|Download_SourceNotFound| Défaut de télécharger la source à partir de stockage Azure - source non trouvée|
|Download_AccessConditionNotSatisfied| Défaut de télécharger la source à partir du stockage Azure - accès refusé|
|Download_Forbidden| Défaut de télécharger la source à partir du stockage Azure - accès interdit|
|Download_AccountNotFound| Défaut de télécharger la source à partir de stockage Azure - compte non trouvé|
|Download_BadRequest| Échec à télécharger la source à partir de stockage Azure - mauvaise demande|
|Download_NotTransient| Défaut de télécharger la source à partir de stockage Azure - pas erreur transitoire|
|Download_UnknownError| Défaut de télécharger la source à partir du stockage Azure - erreur inconnue|
|UpdatePolicy_QuerySchemaDoesNotMatchTableSchema| N’a pas invoque la politique de mise à jour. Le schéma de requête ne correspond pas au schéma de table|
|UpdatePolicy_FailedDescendantTransaction| N’a pas invoque la politique de mise à jour. Politique de mise à jour transactionnelle descendante échouée|
|UpdatePolicy_IngestionError| N’a pas invoque la politique de mise à jour. Erreur d’ingestion s’est produite|
|UpdatePolicy_UnknownError| N’a pas invoque la politique de mise à jour. Une erreur inconnue s'est produite|
|BadRequest_MissingJsonMappingtFailure| Json modèle n’a pas ingéré avec le paramètre jsonMapping|
|BadRequest_InvalidOrEmptyBlob| Blob est invalide ou vide zip archive|
|BadRequest_DatabaseNotExist| La base de données n'existe pas|
|BadRequest_TableNotExist| La table n’existe pas|
|BadRequest_InvalidKustoIdentityToken| Symbolique d’identité kusto invalide|
|BadRequest_UriMissingSas| Blob chemin sans SAS de stockage blob inconnu|
|BadRequest_FileTooLarge| Essayer d’ingérer un fichier trop grand|
|BadRequest_NoValidResponseFromEngine| Aucune réponse valide de la commande ingest|
|BadRequest_TableAccessDenied| L’accès à la table est refusé|
|BadRequest_MessageExhausted| Le message est épuisé|
|General_BadRequest| Mauvaise demande générale (peut faire allusion à l’ingestion à la base de données/table non existante)|
|General_InternalServerError| Erreur interne du serveur s’est produite|

## <a name="detailed-kustoingest-exceptions-reference"></a>Référence détaillée des exceptions Kusto.Ingest

### <a name="cloudqueuesnotfoundexception"></a>CloudQueuesNotFoundException
Soulevé lorsqu’aucune file d’attente n’a été retournée du cluster de gestion des données

Classe de base: [Exception](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Champs :

|Nom|Type|Signification
|-----------|----|------------------------------|
|Error| `String`| L’erreur qui s’est produite alors qu’il tentait de récupérer les files d’attente du DM
                            
Informations supplémentaires :

Pertinent seulement lors de l’utilisation du [Kusto File d’attente Ingérer Client](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient).
Pendant le processus d’ingestion, plusieurs tentatives sont faites pour récupérer les files d’attente Azure liées au DM. Lorsque ces tentatives échouent, l’exception est soulevée contenant la raison de l’échec dans le champ «Erreur» et peut-être une exception interne dans le champ «InnerException».


### <a name="cloudblobcontainersnotfoundexception"></a>CloudBlobContainersNotFoundException
Soulevé lorsqu’aucun conteneur blob n’a été retourné du cluster de gestion des données

Classe de base: [Exception](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Champs :

|Nom|Type|Signification       
|-----------|----|------------------------------|
|KustoEndpoint (KustoEndpoint)| `String`| Le point final du DM pertinent
                            
Informations supplémentaires :

Pertinent seulement lors de l’utilisation du [Kusto File d’attente Ingérer Client](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient).  
Lorsque vous ingrez des sources qui ne sont pas déjà dans un conteneur Azure - c’est-à-dire des fichiers, DataReader ou Stream, les données sont téléchargées sur un blob temporaire pour ingestion. L’exception est soulevée lorsqu’il n’y a pas de conteneurs trouvés pour télécharger les données.

### <a name="duplicateingestionpropertyexception"></a>DuplicateIngestionPropertyException DuplicateIngestionPropertyException DuplicateIngestionPropertyException DuplicateIng
Élevé lorsqu’une propriété d’ingestion est configurée plus d’une fois

Classe de base: [Exception](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Champs :

|Nom|Type|Signification       
|-----------|----|------------------------------|
|PropertyName| `String`| Le nom de la propriété en double
                            
### <a name="postmessagetoqueuefailedexception"></a>PostMessageToQueueFailedException
Soulevé lors de l’affichage d’un message à la file d’attente a échoué

Classe de base: [Exception](https://msdn.microsoft.com/library/system.exception(v=vs.110).aspx)

Champs :

|Nom|Type|Signification       
|-----------|----|------------------------------|
|QueueUri| `String`| L’URI de la file d’attente
|Error| `String`| Le message d’erreur qui a été généré en essayant de poster à la file d’attente
                            
Informations supplémentaires :

Pertinent seulement lors de l’utilisation du [Kusto File d’attente Ingérer Client](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient).  
Le client en file d’attente ingèrent des données en téléchargeant un message sur la file d’attente Azure pertinente. En cas de panne de poste, l’exception est soulevée contenant la file d’attente URI, la raison de l’échec dans le champ «Erreur» et peut-être une exception interne dans le champ «InnerException».

### <a name="dataformatnotspecifiedexception"></a>DataFormatNotSpecifiedException
Augmenté lorsque le format de données est requis, mais non spécifié dans IngestionProperties

Classe de base: IngestClientException

Informations supplémentaires :

Lors de l’ingestion d’un flux, un format de données doit être spécifié dans les [IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties) afin d’ingérer correctement les données. Cette exception est soulevée lorsque l’IngestionProperties.Format n’est pas spécifié.

### <a name="invaliduriingestclientexception"></a>InvalidUriIngestClientException
Élevé lorsqu’un URI tacheté invalide a été présenté comme source d’ingestion

Classe de base: IngestClientException

### <a name="compressfileingestclientexception"></a>CompressFileIngestClientException
Soulevé lorsque le client ingest n’a pas comprimé le fichier prévu pour l’ingestion

Classe de base: IngestClientException

Informations supplémentaires :

Les fichiers sont comprimés avant leur ingestion. Cette exception est soulevée lorsqu’une tentative de compresser le fichier a échoué.

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException
Élevé lorsque le client ingest n’a pas téléchargé la source prévue pour l’ingestion à un blob temporaire

Classe de base: IngestClientException

### <a name="sizelimitexceededingestclientexception"></a>SizeLimitExceedIngestClientException
Augmenté lorsqu’une source d’ingestion est trop grande

Classe de base: IngestClientException

Champs :

|Nom|Type|Signification       
|-----------|----|------------------------------|
|Taille| `long`| La taille de la source d’ingestion
|MaxSize| `long`| La taille maximale permise pour l’ingestion

Informations supplémentaires :

Si une source d’ingestion dépasse la taille maximale de 4 Go, l’exception est lancée. La validation de taille peut être remplacée par le drapeau IgnoreSizeLimit dans la [classe IngestionProperties](kusto-ingest-client-reference.md#class-kustoingestionproperties), mais il n’est pas recommandé [d’ingérer des sources uniques de plus de 1 Go](about-kusto-ingest.md#ingestion-best-practices).

### <a name="uploadfiletotempblobingestclientexception"></a>UploadFileToTempBlobIngestClientException
Élevé lorsque le client ingest n’a pas téléchargé le fichier prévu pour l’ingestion à un blob temporaire

Classe de base: IngestClientException

### <a name="directingestclientexception"></a>DirectIngestClientException
Augmenté lorsqu’une erreur générale s’est produite lors d’une ingestion directe

Classe de base: IngestClientException

### <a name="queuedingestclientexception"></a>File d’attenteIngestClientException
Soulevé lorsqu’une erreur s’est produite lors de l’exécution d’une ingestion en file d’attente

Classe de base: IngestClientException

### <a name="ingestclientaggregateexception"></a>IngestClientAggregateException
Augmenté lorsqu’une ou plusieurs erreurs se sont produites lors d’une ingestion

Classe de base: [AggregateException](https://msdn.microsoft.com/library/system.aggregateexception(v=vs.110).aspx)

Champs :

|Nom|Type|Signification       
|-----------|----|------------------------------|
|IngestionErrors| `IList<IngestClientException>`| Les erreurs qui se sont produites alors qu’elles tentaient d’ingérer et les sources qui les
|IsGlobalError (en)| `bool`| Indique si l’exception s’est produite pour toutes les sources

## <a name="errors-in-native-code"></a>Erreurs dans le code natif
Le moteur Kusto est écrit en code natif. Pour plus de détails sur les erreurs dans le code natif, s’il vous plaît voir [Erreurs dans le code natif](../../concepts/errorsinnativecode.md)