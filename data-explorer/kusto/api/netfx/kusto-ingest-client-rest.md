---
title: Procédure d’ingestion de données sans Kusto. deréception Library-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit comment l’ingestion de données sans Kusto. deréception Library dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9e4be7be65b0fe118a99835b24cd9d69ac5a531d
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108488"
---
# <a name="howto-data-ingestion-without-kustoingest-library"></a>Procédure d’ingestion de données sans Kusto. deréception Library

## <a name="when-to-consider-not-using-kustoingest-library"></a>Quand envisager de ne pas utiliser la bibliothèque Kusto. adsent ?
En règle générale, l’utilisation de la bibliothèque Kusto. deréception doit être recommandée à chaque fois que l’ingestion de données vers Kusto est prise en compte.<BR>
Lorsqu’il ne s’agit pas d’une option (généralement en raison des contraintes du système d’exploitation), il est possible de réaliser pratiquement les mêmes fonctionnalités.<BR>
Cet article explique comment implémenter une ingestion en **file d’attente** sur Kusto sans prendre de dépendance sur le package Kusto. InCharge.

>**Remarque :** Le code ci-dessous est écrit en C# en utilisant le kit de développement logiciel (SDK) stockage Azure, la bibliothèque d’authentification ADAL et le package NewtonSoft. JSON afin de simplifier l’exemple de code.<BR>Si nécessaire, le code correspondant peut être remplacé par des appels d' [API REST Azure Storage](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api) appropriés, un [package non-.net Adal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) et tout package de gestion JSON disponible.

## <a name="overview"></a>Vue d’ensemble
L’exemple de code suivant illustre l’ingestion de données en file d’attente (en passant par le service Kusto Gestion des données) à Kusto sans l’utilisation de la bibliothèque Kusto. deréception.<BR>
Cela peut être utile si le .NET complet est inaccessible ou indisponible en raison d’un environnement ou d’autres restrictions.<BR>

> Cet article traite du mode d’ingestion recommandé pour les pipelines de niveau production, également appelé « ingestion en **attente** » (en termes de la bibliothèque Kusto. inréception, l’entité correspondante est l’interface [IKustoQueuedIngestClient](kusto-ingest-client-reference.md#interface-ikustoqueuedingestclient) ). Dans ce mode, le code client interagit avec le service Kusto en publiant des messages de notification d’ingestion dans une file d’attente Azure, qui est obtenue à partir de la Gestion des données Kusto (également appelée Ingestion). L’interaction avec le service de Gestion des données doit être authentifiée avec **AAD**.

Le plan de ce Flow est décrit dans l’exemple de code ci-dessous et se compose des éléments suivants :
1. Créer un client de stockage Azure et télécharger les données vers un objet BLOB
1. Obtenir le jeton d’authentification pour accéder au service d’ingestion Kusto
2. Interrogez le service d’ingestion Kusto pour obtenir les éléments suivants :
    * Ressources d’ingestion (files d’attente et conteneurs d’objets BLOB)
    * Kusto jeton d’identité qui sera ajouté à chaque message d’ingestion
3. Télécharger des données vers un objet BLOB sur l’un des conteneurs d’objets BLOB obtenus à partir de Kusto dans (2)
4. Compose un message d’ingestion identifiant la base de la table et la base de la cible et pointant vers l’objet blob à partir de (3)
5. Publiez le message d’ingestion que nous avons composé (4) dans l’une des files d’attente d’ingestion obtenues à partir de Kusto dans (2)
6. Récupérer toute erreur rencontrée par le service lors de l’ingestion

Les sections suivantes expliquent chaque étape plus en détail.

```csharp
// A container class for ingestion resources we are going to obtain from Kusto
internal class IngestionResourcesSnapshot
{
    public IList<string> IngestionQueues { get; set; } = new List<string>();
    public IList<string> TempStorageContainers { get; set; } = new List<string>();

    public string FailureNotificationsQueue { get; set; } = string.Empty;
    public string SuccessNotificationsQueue { get; set; } = string.Empty;
}

public static void IngestSingleFile(string file, string db, string table, string ingestionMappingRef)
{
    // Your Kusto ingestion service URI, typically ingest-<your cluster name>.kusto.windows.net
    string DmServiceBaseUri = @"https://ingest-{serviceNameAndRegion}.kusto.windows.net";

    // 1. Authenticate the interactive user (or application) to access Kusto ingestion service
    string bearerToken = AuthenticateInteractiveUser(DmServiceBaseUri);

    // 2a. Retrieve ingestion resources
    IngestionResourcesSnapshot ingestionResources = RetrieveIngestionResources(DmServiceBaseUri, bearerToken);

    // 2b. Retrieve Kusto identity token
    string identityToken = RetrieveKustoIdentityToken(DmServiceBaseUri, bearerToken);

    // 3. Upload file to one of the blob containers we got from Kusto.
    // This example uses the first one, but when working with multiple blobs,
    // one should round-robin the containers in order to prevent throttling
    long blobSizeBytes = 0;
    string blobName = $"TestData{DateTime.UtcNow.ToString("yyyy-MM-dd_HH-mm-ss.FFF")}";
    string blobUriWithSas = UploadFileToBlobContainer(file, ingestionResources.TempStorageContainers.First(),
                                                            "temp001", blobName, out blobSizeBytes);

    // 4. Compose ingestion command
    string ingestionMessage = PrepareIngestionMessage(db, table, blobUriWithSas, blobSizeBytes, ingestionMappingRef, identityToken);

    // 5. Post ingestion command to one of the ingestion queues we got from Kusto.
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

## <a name="1-obtain-authentication-evidence-from-aad"></a>1. obtenir les preuves d’authentification à partir d’AAD
Ici, nous utilisons ADAL pour obtenir un jeton AAD pour accéder au service de Gestion des données Kusto afin de demander ses files d’attente d’entrée.
ADAL est disponible sur [les plateformes non-Windows](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) , si nécessaire.
```csharp
// Authenticates the interactive user and retrieves AAD Access token for specified resource
internal static string AuthenticateInteractiveUser(string resource)
{
    // Create Auth Context for MSFT AAD:
    AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/{AAD Tenant ID or name}");

    // Acquire user token for the interactive user for Kusto:
    AuthenticationResult result =
        authContext.AcquireTokenAsync(resource, "<your client app ID>", new Uri(@"<your client app URI>"),
                                        new PlatformParameters(PromptBehavior.Auto), UserIdentifier.AnyUser, "prompt=select_account").Result;
    return result.AccessToken;
}
```

## <a name="2-retrieve-kusto-ingestion-resources"></a>2. récupérer les ressources d’ingestion Kusto
C’est là que les choses sont intéressantes. Ici, nous créons manuellement une requête HTTP Après Kusto service Gestion des données, en demandant à retourner les ressources d’ingestion.
Celles-ci incluent les files d’attente sur lesquelles le service DM écoute, ainsi que les conteneurs d’objets BLOB pour le téléchargement de données.
Le service Gestion des données traite tout message contenant des demandes d’ingestion qui arrivent sur l’une de ces files d’attente.
```csharp
// Retrieve ingestion resources (queues and blob containers) with SAS from specified Kusto Ingestion service using supplied Access token
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

## <a name="obtaining-kusto-identity-token"></a>Obtention du jeton d’identité Kusto
L’une des étapes importantes de l’autorisation de l’ingestion de données consiste à obtenir un jeton d’identité et à l’attacher à chaque message de réception. Comme les messages de réception sont transmis à Kusto via un canal non direct (file d’attente Azure), il n’existe aucun moyen d’effectuer la validation d’autorisation intrabande. Le mécanisme de jeton d’identité le permet en émettant une preuve d’identité signée par Kusto qui peut être validée par le service Kusto une fois qu’il a reçu le message d’ingestion.
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

## <a name="3-upload-data-to-azure-blob-container"></a>3. charger des données dans le conteneur d’objets BLOB Azure
Cette étape consiste à télécharger un fichier local vers un objet BLOB Azure qui sera ensuite remis pour réception. Ce code utilise le kit de développement logiciel (SDK) stockage Azure, mais dans ce cas, il est possible d’obtenir le même accès avec l' [API REST du service BLOB Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/blob-service-rest-api).
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

## <a name="4-compose-kusto-ingestion-message"></a>4. composer le message d’ingestion Kusto
Ici, nous utilisons à nouveau le package NewtonSoft. JSON pour composer un message de demande d’ingestion valide qui sera publié dans la file d’attente Azure sur laquelle le service de Gestion des données Kusto approprié écoute.
* Il s’agit de la valeur minimale pour le message d’ingestion.
* Notez que ce jeton d’identité est obligatoire et doit résider dans `AdditionalProperties` l’objet JSON.
* Chaque fois que `CsvMapping` nécessaire, `JsonMapping` une propriété ou doit également être fournie.
* Pour plus d’informations, consultez [l’article sur la pré-création du mappage](../../management/create-ingestion-mapping-command.md) d’ingestion
* L' [annexe A](#appendix-a-ingestion-message-internal-structure) fournit une explication sur la structure des messages d’ingestion

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

## <a name="5-post-kusto-ingestion-message-to-kusto-ingestion-queue"></a>5. envoi du message d’ingestion Kusto à la file d’attente d’ingestion Kusto
Enfin, l’agit proprement dit : il suffit de poster le message que nous avons créé dans la file d’attente que nous avons choisie.<BR>
Remarque : lors de l’utilisation du client de stockage .net, il encode le message en base64 par défaut. Veuillez consulter les [documents de stockage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.encodemessage).<BR>
Si vous n’utilisez pas ce client, assurez-vous d’encoder correctement le contenu du message.

```csharp
internal static void PostMessageToQueue(string queueUriWithSas, string message)
{
    CloudQueue queue = new CloudQueue(new Uri(queueUriWithSas));
    CloudQueueMessage queueMessage = new CloudQueueMessage(message);

    queue.AddMessage(queueMessage, null, null, null, null);
}
```

## <a name="6-pop-messages-from-an-azure-queue"></a>6. messages pop d’une file d’attente Azure
Après réception, nous utilisons cette méthode pour lire les messages d’échec de la file d’attente appropriée écrite par Kusto Gestion des données service.<BR>
L' [annexe B](#appendix-b-ingestion-failure-message-structure) fournit des explications sur la structure des messages d’échec

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

## <a name="appendix-a-ingestion-message-internal-structure"></a>Annexe A : structure interne du message d’ingestion
Le message que Kusto Gestion des données service attend à lire à partir de la file d’attente Azure est un document JSON au format suivant :

```json
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
|BlobPath |URI d’objet BLOB, y compris la clé SAS qui accorde des autorisations Kusto pour le lire/l’écrire/le supprimer (les autorisations d’écriture/suppression sont requises si Kusto est de supprimer l’objet BLOB une fois qu’il a terminé la réception des données) |
|RawDataSize |Taille des données non compressées en octets. La fourniture de cette valeur permet à Kusto d’optimiser l’ingestion en regroupant potentiellement plusieurs objets BLOB. Cette propriété est facultative, mais si elle n’est pas fournie, Kusto accède à l’objet BLOB juste pour récupérer la taille |
|nom_base_de_données |Nom de la base de données cible |
|TableName |Nom de la table cible |
|RetainBlobOnSuccess |Si la valeur `true`est, l’objet BLOB n’est pas supprimé une fois que l’ingestion s’est terminée avec succès. La valeur par défaut est `false` |
|Format |Format de données non compressées |
|FlushImmediately |Si la valeur `true`est, toute agrégation sera ignorée. La valeur par défaut est `false` |
|ReportLevel |Niveau de rapport de réussite/erreur : 0-échecs, 1-aucun, 2-tout |
|ReportMethod |Mécanisme de création de rapports : 0-file d’attente, 1-table |
|AdditionalProperties |Toutes les propriétés supplémentaires (balises, etc.) |


## <a name="appendix-b-ingestion-failure-message-structure"></a>Annexe B : structure des messages d’échec d’ingestion
Le message de tableau suivant que Kusto Gestion des données service s’attend à lire à partir de la file d’attente Azure est un document JSON au format suivant :

|Propriété | Description |
|---------|-------------
|OperationId |Identificateur de l’opération (GUID) qui peut être utilisé pour effectuer le suivi de l’opération côté service |
|Base de données |Nom de la base de données cible |
|Table de charge de travail |Nom de la table cible |
|FailedOn |Horodateur d’échec |
|IngestionSourceId |GUID identifiant le segment de données dont la réception par Kusto a échoué |
|IngestionSourcePath |Chemin d’accès (URI) au segment de données dont la réception par Kusto a échoué |
|Détails |Message d’échec |
|ErrorCode |Code d’erreur Kusto (Voir tous les codes d’erreur [ici](kusto-ingest-client-errors.md#ingestion-error-codes)) |
|FailureStatus |Indique si l’échec est permanent ou temporaire |
|RootActivityId |Identificateur de corrélation Kusto (GUID) qui peut être utilisé pour effectuer le suivi de l’opération côté service |
|OriginatesFromUpdatePolicy |Indique si l’échec est dû à une [stratégie de mise à jour transactionnelle](../../management/updatepolicy.md) erroné |
|ShouldRetry | Indique si l’ingestion peut être effectuée si une nouvelle tentative est effectuée |