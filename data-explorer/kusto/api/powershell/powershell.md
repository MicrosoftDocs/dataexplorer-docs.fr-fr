---
title: Bibliothèques clientes .NET Kusto à partir de PowerShell-Azure Explorateur de données
description: Cet article décrit l’utilisation des bibliothèques clientes .NET à partir de PowerShell dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: b454b9453c7afd0835041ac78d13318de73432e2
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382063"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>Utilisation des bibliothèques clientes .NET à partir de PowerShell

Les scripts PowerShell peuvent utiliser des bibliothèques clientes Azure Explorateur de données .NET par le biais de l’intégration intégrée de PowerShell avec des bibliothèques .NET arbitraires (non-PowerShell).

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>Obtention des bibliothèques clientes .NET pour l’écriture de scripts avec PowerShell

Pour commencer à utiliser les bibliothèques clientes Azure Explorateur de données .NET à l’aide de PowerShell.

1. Téléchargez le [ `Microsoft.Azure.Kusto.Tools` package NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).
1. Extrayez le contenu du répertoire « tools » dans le package (à l’aide d’un outil d’archivage comme `7-zip` ).
1. Appelez `[System.Reflection.Assembly]::LoadFrom("path")` à partir de PowerShell pour charger la bibliothèque requise. 
    - Le `path` paramètre de la commande doit indiquer l’emplacement des fichiers extraits.
1. Une fois tous les assemblys .NET dépendants chargés :
   1. Créez une chaîne de connexion Kusto.
   1. Instanciez un *fournisseur de requêtes* ou un fournisseur d' *administration*.
   1. Exécutez les requêtes ou les commandes, comme indiqué dans les [exemples](powershell.md#examples) ci-dessous.

Pour plus d’informations, consultez les [bibliothèques clientes Azure Explorateur de données](../netfx/about-kusto-data.md).

## <a name="examples"></a>Exemples

### <a name="initialization"></a>Initialisation

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example of the location from where you extract the Microsoft.Azure.Kusto.Tools package.
#  Please make sure you load the types from a local directory and not from a remote share.
$packagesRoot = "C:\Microsoft.Azure.Kusto.Tools\Tools"

#  Part 2 of 3
#  ------------
#  Loading the Kusto.Client library and its dependencies
dir $packagesRoot\* | Unblock-File
[System.Reflection.Assembly]::LoadFrom("$packagesRoot\Kusto.Data.dll")

#  Part 3 of 3
#  ------------
#  Defining the connection to your cluster / database
$clusterUrl = "https://help.kusto.windows.net;Fed=True"
$databaseName = "Samples"

#   Option A: using Azure AD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using Azure AD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
```

### <a name="example-running-an-admin-command"></a>Exemple : exécution d’une commande d’administration

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

Et la sortie est la suivante :
```
IsHealthy = True
```

### <a name="example-running-a-query"></a>Exemple : exécution d’une requête

```powershell
$queryProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslQueryProvider($kcsb)
$query = "StormEvents | limit 5"
Write-Host "Executing query: '$query' with connection string: '$($kcsb.ToString())'"
#   Optional: set a client request ID and set a client request property (e.g. Server Timeout)
$crp = New-Object Kusto.Data.Common.ClientRequestProperties
$crp.ClientRequestId = "MyPowershellScript.ExecuteQuery." + [Guid]::NewGuid().ToString()
$crp.SetOption([Kusto.Data.Common.ClientRequestProperties]::OptionServerTimeout, [TimeSpan]::FromSeconds(30))

#   Execute the query
$reader = $queryProvider.ExecuteQuery($query, $crp)

# Do something with the result datatable, for example: print it formatted as a table, sorted by the 
# "StartTime" column, in descending order
$dataTable = [Kusto.Cloud.Platform.Data.ExtendedDataReader]::ToDataSet($reader).Tables[0]
$dataView = New-Object System.Data.DataView($dataTable)
$dataView | Sort StartTime -Descending | Format-Table -AutoSize
```

Et la sortie est la suivante :

|StartTime           |EndTime             |EpisodeID |EventID |État          |Type d’événement         |InjuriesDirect |InjuriesIndirect |DeathsDirect |DeathsIndirect
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |Géorgie        |Vent d’orage |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |MISSISSIPPI    |Vent d’orage |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |SUD DE L’ATLANTIQUE |Bec d’eau       |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |Floride        |Tornade           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |Floride        |Pluie lourde        |             0 |               0 |           0 |             0
