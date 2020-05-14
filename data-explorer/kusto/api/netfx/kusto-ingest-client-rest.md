---
title: Ingestion de données Kusto sans bibliothèque de réception-Azure Explorateur de données
description: Cet article décrit comment l’ingestion de données sans Kusto. deréception Library dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.custom: has-adal-ref
ms.date: 02/19/2020
ms.openlocfilehash: 96409849823850ef9fd939f9e359d75d3e6d5bf1
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382146"
---
# <a name="ingestion-without-kustoingest-library"></a>Ingestion sans la bibliothèque Kusto. deréception

La bibliothèque Kusto. Adout est préférable pour la réception de données dans Azure Explorateur de données. Toutefois, vous pouvez toujours atteindre presque la même fonctionnalité, sans dépendre du package Kusto. inversion.
Cet article vous montre comment utiliser l’ingestion en *attente* dans Azure Explorateur de données pour les pipelines de niveau production.

> [!NOTE]
> Le code ci-dessous est écrit en C# et utilise le kit de développement logiciel (SDK) stockage Azure, la bibliothèque d’authentification ADAL et le package NewtonSoft. JSON pour simplifier l’exemple de code. Si nécessaire, le code correspondant peut être remplacé par des appels d' [API REST Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) appropriés, un [package non-.net Adal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)et tout package de gestion JSON disponible.

Cet article traite du mode de réception recommandé. Pour la bibliothèque Kusto. deréception, son entité correspondante est l’interface [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) . Ici, le code client interagit avec le service Azure Explorateur de données en publiant des messages de notification d’ingestion dans une file d’attente Azure. Les références aux messages sont obtenues à partir du service Kusto Gestion des données (également connu sous le nom de « réception »). L’interaction avec le service doit être authentifiée à l’aide de Azure Active Directory (Azure AD).

Le code suivant montre comment le service de Gestion des données Kusto gère l’ingestion des données mises en file d’attente sans utiliser la bibliothèque Kusto. deréception. Cet exemple peut être utile si le .NET complet est inaccessible ou indisponible en raison de l’environnement, ou d’autres restrictions.

Le code comprend les étapes permettant de créer un client de stockage Azure et de télécharger les données vers un objet BLOB.
Chaque étape est décrite plus en détail, après l’exemple de code.

1. [Obtenir un jeton d’authentification pour accéder au service d’ingestion d’Explorateur de données Azure](#obtain-authentication-evidence-from-azure-ad)
1. Interrogez le service d’ingestion d’Azure Explorateur de données pour obtenir :
    * [Ressources d’ingestion (files d’attente et conteneurs d’objets BLOB)](#retrieve-azure-data-explorer-ingestion-resources)
    * [Un jeton d’identité Kusto qui sera ajouté à chaque message d’ingestion](#obtain-a-kusto-identity-token)
1. [Télécharger des données vers un objet BLOB sur l’un des conteneurs d’objets BLOB obtenus à partir de Kusto dans (2)](#upload-data-to-the-azure-blob-container)
1. [Compose un message d’ingestion qui identifie la base de données et la table cibles et qui pointe vers l’objet blob à partir de (3)](#compose-the-azure-data-explorer-ingestion-message)
1. [Publiez le message d’ingestion que nous avons composé (4) dans une file d’attente d’ingestion obtenue à partir d’Azure Explorateur de données dans (2)](#post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue)**
1. [Récupérer toute erreur détectée par le service lors de l’ingestion](#check-for-error-messages-from-the-azure-queue)

```csharp
// A container class for ingestion resources we are going to obtain from Azure Data Explorer
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Azure Data Explorer ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Azure Data Explorer.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Azure Data Explorer.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the queues in order to prevent throttling
    PostMessageToQueue(ingestionResources.IngestionQueues.First(), ingestionMessage);

    Thread.Sleep(20000);

    // 6a. Read success notifications
    var successes = PopTopMessagesFromQueue(ingestionResources.SuccessNotificationsQueue, 32);
    foreach (var sm in successes)
    {
        Console.WriteLine($"Ingestion completed: {sm}");
    }

    // 6b. Read failure notifications
    var errors = PopTopMessagesFromQueue(ingestionResources.FailureNotificationsQueue, 32);
    foreach (var em in errors)
    {
        Console.WriteLine($"Ingestion error: {em}");
    }
}
```

## <a name="using-queued-ingestion-to-azure-data-explorer-for-production-grade-pipelines"></a>Utilisation de la réception en file d’attente dans Azure Explorateur de données pour les pipelines de niveau production

### <a name="obtain-authentication-evidence-from-azure-ad"></a>Obtenir des preuves d’authentification à partir de Azure AD

Ici, nous utilisons ADAL pour obtenir un jeton Azure AD pour accéder au service Kusto Gestion des données et demander ses files d’attente d’entrée.
ADAL est disponible sur [les plateformes non-Windows](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) , si nécessaire.

```csharp
// Authenticates the interactive user and retrieves Azure AD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT Azure AD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{Azure AD Tenant ID or name}");

    // Acquire user token for the interactive user for Azure Data Explorer:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

### <a name="retrieve-azure-data-explorer-ingestion-resources"></a>Récupérer les ressources d’ingestion d’Azure Explorateur de données

Construisez manuellement une requête HTTP Après le service Gestion des données, en demandant le retour des ressources d’ingestion. Ces ressources incluent les files d’attente sur lesquelles le service DM écoute et les conteneurs d’objets BLOB pour le téléchargement de données.
Le service Gestion des données traite tous les messages contenant des demandes d’ingestion qui arrivent sur l’une de ces files d’attente.

```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Azure Data Explorer Ingestion service using supplied Access token
internal static IngestionResourcesSnapshot RetrieveIngestionResources(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get ingestion resources\" }}";

    IngestionResourcesSnapshot ingestionResources = new IngestionResourcesSnapshot();

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        IEnumerable<JToken> tokens;

        // Input queues
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SecuredReadyForAggregationQueue')]");
        foreach (var token in tokens)
        {
            ingestionResources.IngestionQueues.Add((string) token[1]);
        }

        // Temp storage containers
        tokens = responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'TempStorage')]");
        foreach (var token in tokens)
        {
            ingestionResources.TempStorageContainers.Add((string)token[1]);
        }

        // Failure notifications queue
        var singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'FailedIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.FailureNotificationsQueue = (string)singleToken;

        // Success notifications queue
        singleToken =
            responseJson.SelectTokens("Tables[0].Rows[?(@.[0] == 'SuccessfulIngestionsQueue')].[1]").FirstOrDefault();
        ingestionResources.SuccessNotificationsQueue = (string)singleToken;
    }

    return ingestionResources;
}

// Executes a POST request on provided URI using supplied Access token and request body
internal static WebResponse SendPostRequest(string uriString, string authToken, string body)
{
    WebRequest request = WebRequest.Create(uriString);

    request.Method = "POST";
    request.ContentType = "application/json";
    request.ContentLength = body.Length;
    request.Headers.Set(HttpRequestHeader.Authorization, $"Bearer {authToken}");

    Stream bodyStream = request.GetRequestStream();
    using (StreamWriter sw = new StreamWriter(bodyStream))
    {
        sw.Write(body);
        sw.Flush();
    }

    bodyStream.Close();
    return request.GetResponse();
}
```

### <a name="obtain-a-kusto-identity-token"></a>Obtenir un jeton d’identité Kusto

Les messages de réception sont transmis à Azure Explorateur de données via un canal non direct (file d’attente Azure), ce qui rend impossible la validation d’autorisation intrabande pour l’accès au service d’ingestion de Explorateur de données Azure. La solution consiste à attacher un jeton d’identité à chaque message de réception. Le jeton active la validation d’autorisation intrabande. Ce jeton signé peut ensuite être validé par le service Azure Explorateur de données lorsqu’il reçoit le message d’ingestion.

```csharp
// Retrieves a Kusto identity token that will be added to every ingest message
internal static string RetrieveKustoIdentityToken(string ingestClusterBaseUri, string accessToken)
{
    string ingestClusterUri = $"{ingestClusterBaseUri}/v1/rest/mgmt";
    string requestBody = $"{{ \"csl\": \".get kusto identity token\" }}";
    string jsonPath = "Tables[0].Rows[*].[0]";

    using (WebResponse response = SendPostRequest(ingestClusterUri, accessToken, requestBody))
    using (StreamReader sr = new StreamReader(response.GetResponseStream()))
    using (JsonTextReader jtr = new JsonTextReader(sr))
    {
        JObject responseJson = JObject.Load(jtr);
        JToken identityToken = responseJson.SelectTokens(jsonPath).FirstOrDefault();

        return ((string)identityToken);
    }
}
```

### <a name="upload-data-to-the-azure-blob-container"></a>Charger des données dans le conteneur d’objets BLOB Azure

Cette étape consiste à télécharger un fichier local vers un objet BLOB Azure qui sera remis à l’ingestion. Ce code utilise le kit de développement logiciel (SDK) Azure Storage. Si la dépendance n’est pas possible, elle peut être obtenue avec l' [API REST du service BLOB Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api).

```csharp
// Uploads a single local file to an Azure Blob container, returns blob URI and original data size
internal static string UploadFileToBlobContainer(string filePath, string blobContainerUri, string containerName, string blobName, out long blobSize)
{
    var blobUri = new Uri(blobContainerUri);
    CloudBlobContainer blobContainer = new CloudBlobContainer(blobUri);
    CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference(blobName);

    using (Stream stream = File.OpenRead(filePath))
    {
        blockBlob.UploadFromStream(stream);
        blobSize = blockBlob.Properties.Length;
    }

    return string.Format("{0}{1}", blockBlob.Uri.AbsoluteUri, blobUri.Query);
}
```

### <a name="compose-the-azure-data-explorer-ingestion-message"></a>Composer le message d’ingestion d’Azure Explorateur de données

Le package NewtonSoft. JSON compose à nouveau une demande d’ingestion valide pour identifier la base de données et la table cibles, et pointe vers l’objet BLOB.
Le message est publié dans la file d’attente Azure sur laquelle le service de Gestion des données Kusto approprié écoute.

Voici quelques points à prendre en compte.

* Cette demande est le minimum minimal pour le message d’ingestion.

> [!NOTE]
> Le jeton d’identité est obligatoire et doit faire partie de l’objet JSON **AdditionalProperties** .

* Chaque fois que nécessaire, les propriétés CsvMapping ou JsonMapping doivent également être fournies.
* Pour plus d’informations, consultez l' [article sur la pré-création du mappage de](../../management/create-ingestion-mapping-command.md)l’ingestion.
* Section la [structure interne du message d’ingestion](#ingestion-message-internal-structure) fournit une explication de la structure du message d’ingestion

```csharp
internal static string PrepareIngestionMessage(string db, string table, string dataUri, long blobSizeBytes, string mappingRef, string identityToken)
{
    var message = new JObject();

    message.Add("Id", Guid.NewGuid().ToString());
    message.Add("BlobPath", dataUri);
    message.Add("RawDataSize", blobSizeBytes);
    message.Add("DatabaseName", db);
    message.Add("TableName", table);
    message.Add("RetainBlobOnSuccess", true);   // Do not delete the blob on success
    message.Add("Format", "json");              // Data is in JSON format
    message.Add("FlushImmediately", true);      // Do not aggregate
    message.Add("ReportLevel", 2);              // Report failures and successes (might incur perf overhead)
    message.Add("ReportMethod", 0);             // Failures are reported to an Azure Queue

    message.Add("AdditionalProperties", new JObject(
                                            new JProperty("authorizationContext", identityToken),
                                            new JProperty("jsonMappingReference", mappingRef)));
    return message.ToString();
}
```

### <a name="post-the-azure-data-explorer-ingestion-message-to-the-azure-data-explorer-ingestion-queue"></a>Publication du message d’ingestion Azure Explorateur de données dans la file d’attente d’ingestion d’Azure Explorateur de données

Enfin, publiez le message que vous avez créé dans la file d’attente d’ingestion sélectionnée que vous avez obtenue à partir d’Azure Explorateur de données.

> [!NOTE]
> Le client de stockage .net, lorsqu’il est utilisé, encode le message au format Base64 par défaut. Pour plus d’informations, consultez la documentation relative au [stockage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueue.encodemessage?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Queue_CloudQueue_EncodeMessage). Si vous n’utilisez pas ce client, assurez-vous de coder correctement le contenu du message.

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

### <a name="check-for-error-messages-from-the-azure-queue"></a>Rechercher les messages d’erreur de la file d’attente Azure

Après réception, nous vérifions les messages d’échec de la file d’attente appropriée dans laquelle le Gestion des données écrit. Pour plus d’informations sur la structure des messages d’échec, consultez [structure des messages d’échec d’ingestion](#ingestion-failure-message-structure). 

```csharp
internal static IEnumerable<string> PopTopMessagesFromQueue(string queueUriWithSas, int count)
{
    List<string> messages = Enumerable.Empty<string>().ToList();
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    var messagesFromQueue = queue.GetMessages(count);
    foreach (var m in messagesFromQueue)
    {
        messages.Add(m.AsString);
        queue.DeleteMessage(m);
    }

    return messages;
}
```

## <a name="ingestion-messages---json-document-formats"></a>Messages d’ingestion-formats de document JSON

### <a name="ingestion-message-internal-structure"></a>Structure interne du message d’ingestion

Le message que le service Gestion des données Kusto attend à lire à partir de la file d’attente Azure est un document JSON au format suivant.

```JSON
{
    "Id" : "<Id>",
    "BlobPath" : "https://<AccountName>.blob.core.windows.net/<ContainerName>/<PathToBlob>?<SasToken>",
    "RawDataSize" : "<RawDataSizeInBytes>",
    "DatabaseName": "<DatabaseName>",
    "TableName" : "<TableName>",
    "RetainBlobOnSuccess" : "<RetainBlobOnSuccess>",
    "Format" : "<csv|tsv|...>",
    "FlushImmediately": "<true|false>",
    "ReportLevel" : <0-Failures, 1-None, 2-All>,
    "ReportMethod" : <0-Queue, 1-Table>,
    "AdditionalProperties" : { "<PropertyName>" : "<PropertyValue>" }
}
```

|Propriété | Description |
|---------|-------------|
|Id |Identificateur de message (GUID) |
|BlobPath |Chemin (URI) de l’objet BLOB, y compris la clé SAS accordant à Azure Explorateur de données des autorisations de lecture/écriture/suppression. Des autorisations sont requises pour permettre à Azure Explorateur de données de supprimer l’objet BLOB une fois qu’il a terminé la réception des données|
|RawDataSize |Taille des données non compressées en octets. La fourniture de cette valeur permet à Azure Explorateur de données d’optimiser l’ingestion en regroupant potentiellement plusieurs objets BLOB. Cette propriété est facultative, mais si elle n’est pas spécifiée, Azure Explorateur de données accède à l’objet BLOB juste pour récupérer la taille |
|nom_base_de_données |Nom de la base de données cible |
|TableName |Nom de la table cible |
|RetainBlobOnSuccess |Si la valeur `true` est, l’objet BLOB n’est pas supprimé une fois que l’ingestion s’est terminée avec succès. La valeur par défaut est `false` |
|Format |Format de données non compressées |
|FlushImmediately |Si la valeur est `true` , toute agrégation sera ignorée. La valeur par défaut est `false` |
|ReportLevel |Niveau de rapport de réussite/erreur : 0-échecs, 1-aucun, 2-tout |
|ReportMethod |Mécanisme de création de rapports : 0-file d’attente, 1-table |
|AdditionalProperties |Propriétés supplémentaires telles que les balises |

### <a name="ingestion-failure-message-structure"></a>Structure du message d’échec d’ingestion

Le message que le Gestion des données attend à lire à partir de la file d’attente Azure d’entrée est un document JSON au format suivant.

|Propriété | Description |
|---------|-------------
|OperationId |Identificateur de l’opération (GUID) qui peut être utilisé pour effectuer le suivi de l’opération côté service |
|Base de données |Nom de la base de données cible |
|Table de charge de travail |Nom de la table cible |
|FailedOn |Horodateur d’échec |
|IngestionSourceId |GUID identifiant le segment de données qu’Azure Explorateur de données n’a pas pu ingérer |
|IngestionSourcePath |Chemin d’accès (URI) au segment de données qu’Azure Explorateur de données n’a pas pu ingérer |
|Détails |Message d’échec |
|ErrorCode |Code d’erreur Azure Explorateur de données (Voir tous les codes d’erreur [ici](kusto-ingest-client-errors.md#ingestion-error-codes)) |
|FailureStatus |Indique si l’échec est permanent ou temporaire |
|RootActivityId |Identificateur de corrélation Azure Explorateur de données qui peut être utilisé pour effectuer le suivi de l’opération côté service |
|OriginatesFromUpdatePolicy |Indique si l’échec est dû à une [stratégie de mise à jour transactionnelle](../../management/updatepolicy.md) erronée |
|ShouldRetry | Indique si l’ingestion peut être effectuée si une nouvelle tentative est effectuée |
