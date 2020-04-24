---
title: Bibliothèque cliente Kusto - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la bibliothèque cliente Kusto dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/15/2020
ms.openlocfilehash: 8a3ab9bb3727efdc80e1887ab802f2ad4e3c7cce
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81502599"
---
# <a name="kusto-client-library"></a>Bibliothèque cliente Kusto
    
Le Kusto Client SDK (Kusto.Data) expose une API programmatique similaire à ADO.NET, de sorte que l’utilisation devrait se sentir naturel pour ceux qui ont connu avec .NET. Vous créez soit un`ICslQueryProvider`client de requête (`ICslAdminProvider`) ou un fournisseur de commande de contrôle ( ) à partir d’un objet de chaîne de connexion pointant vers le service moteur Kusto, base de données, méthode d’authentification, etc. Vous pouvez ensuite émettre des requêtes de données ou des commandes de contrôle en spécifiant `IDataReader` la chaîne de langage de requête Kusto appropriée, et récupérer un ou plusieurs tableaux de données via l’objet retourné.

Plus concrètement, pour créer un client ADO.NET permettant des requêtes contre Kusto, utilisez des méthodes statiques sur la `Kusto.Data.Net.Client.KustoClientFactory` classe. Ceux-ci prennent la chaîne de connexion et créent un thread-sûr, jetable, objet client. (Il est fortement recommandé que le code client ne crée pas "trop" d’instances de cet objet. Au lieu de cela, le code client devrait créer un objet par chaîne de connexion et s’y accrocher aussi longtemps que nécessaire.) Cela permet à l’objet client de mettre en cache efficacement les ressources.

En général, toutes les méthodes sur les clients `Dispose`sont sans fil à deux exceptions près: , et les propriétés setter. Pour des résultats cohérents, n’invoquez pas les deux méthodes en même temps.

Voici quelques exemples. D’autres échantillons peuvent être trouvés [ici](https://github.com/Azure/azure-kusto-samples-dotnet/tree/master/client).

**Exemple : Comptage des rangées**
 
Le code suivant montre le comptage des `StormEvents` lignes d’une table nommée dans une base de données nommée `Samples`:

```csharp
var client = Kusto.Data.Net.Client.KustoClientFactory.CreateCslQueryProvider("https://help.kusto.windows.net/Samples;Fed=true");
var reader = client.ExecuteQuery("StormEvents | count");
// Read the first row from reader -- it's 0'th column is the count of records in MyTable
// Don't forget to dispose of reader when done.
```

**Exemple : Obtenir des informations diagnostiques du cluster Kusto**

```csharp
var kcsb = new KustoConnectionStringBuilder(cluster URI here). WithAadUserPromptAuthentication();
using (var client = KustoClientFactory.CreateCslAdminProvider(kcsb))
{
    var diagnosticsCommand = CslCommandGenerator.GenerateShowDiagnosticsCommand();
    var objectReader = new ObjectReader<DiagnosticsShowCommandResult>(client.ExecuteControlCommand(diagnosticsCommand));
    DiagnosticsShowCommandResult diagResult = objectReader.ToList().FirstOrDefault();
    // DO something with the diagResult    
}
```



## <a name="the-kustoclientfactory-client-factory"></a>L’usine cliente de KustoClientFactory

La classe `Kusto.Data.Net.Client.KustoClientFactory` statique fournit le principal point d’entrée pour les auteurs de code client qui utilise Kusto. Il fournit les méthodes statiques importantes suivantes :

|Méthode                                      |Retours                                |Utilisé pour                                                      |
|--------------------------------------------|---------------------------------------|--------------------------------------------------------------|
|`CreateCslQueryProvider`                    |`ICslQueryProvider`                    |Envoi de requêtes à un cluster moteur Kusto.                    |
|`CreateCslAdminProvider`                    |`ICslAdminProvider`                    |Envoi de commandes de contrôle à un cluster Kusto (de toute nature).    |
|`CreateRedirectProvider`                    |`IRedirectProvider`                    |Création d’un message de réponse HTTP redirection pour une demande Kusto.|

