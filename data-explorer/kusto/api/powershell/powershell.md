---
title: Utilisation des bibliothèques clients .NET de PowerShell - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’utilisation des bibliothèques clientes .NET de PowerShell dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 05/29/2019
ms.openlocfilehash: 635a23021a1a8c30347bfa27ecd65886b46a6fea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81503211"
---
# <a name="using-the-net-client-libraries-from-powershell"></a>Utilisation des bibliothèques clientes .NET de PowerShell

Les bibliothèques de clients Azure Data Explorer .NET peuvent être utilisées par les scripts PowerShell grâce à l’intégration intégrée de PowerShell avec des bibliothèques arbitraires (non PowerShell).

## <a name="getting-the-net-client-libraries-for-scripting-with-powershell"></a>Obtenir les bibliothèques de clients .NET pour script avec PowerShell

Pour commencer à travailler avec les bibliothèques de clients Azure Data Explorer .NET utilisant PowerShell :

1. Télécharger le [ `Microsoft.Azure.Kusto.Tools` forfait NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/).
2. Extraire le contenu de l’annuaire des outils dans l’emballage (à l’aide de 7 zips, par exemple).
3. Appelez `[System.Reflection.Assembly]::LoadFrom("path")` à partir de powershell, pour charger la bibliothèque requise. 
    - Le `"path"` paramètre de la commande doit indiquer l’emplacement des fichiers extraits.
4. Une fois que tous les assemblages .NET dépendants sont chargés, créez une chaîne de connexion Kusto, instantanéisez un *fournisseur de requête* ou un fournisseur *d’administration,* et exécutez les requêtes ou commandes (comme indiqué dans les [exemples](powershell.md#examples) ci-dessous).

Pour plus d’informations, consultez les [bibliothèques de clients Azure Data Explorer](../netfx/about-kusto-data.md).

## <a name="examples"></a>Exemples

### <a name="initialization"></a>Initialisation

```powershell
#  Part 1 of 3
#  ------------
#  Packages location - This is an example to the location where you extract the Microsoft.Azure.Kusto.Tools package.
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

#   Option A: using AAD User Authentication
$kcsb = New-Object Kusto.Data.KustoConnectionStringBuilder ($clusterUrl, $databaseName)
 
#   Option B: using AAD application Authentication
#     $applicationId = "application ID goes here"
#     $applicationKey = "application key goes here"
#     $authority = "authority goes here"
#     $kcsb = $kcsb.WithAadApplicationKeyAuthentication($applicationId, $applicationKey, $authority)
```

### <a name="example-running-an-admin-command"></a>Exemple : Exécution d’une commande d’administrateur

```powershell
$adminProvider = [Kusto.Data.Net.Client.KustoClientFactory]::CreateCslAdminProvider($kcsb)
$command = [Kusto.Data.Common.CslCommandGenerator]::GenerateDiagnosticsShowCommand()
Write-Host "Executing command: '$command' with connection string: '$($kcsb.ToString())'"
$reader = $adminProvider.ExecuteControlCommand($command)
$reader.Read() # this reads a single row/record. If you have multiple ones returned, you can read in a loop 
$isHealthy = $Reader.GetBoolean(0)
Write-Host "IsHealthy = $isHealthy"
```

Et la sortie est:
```
IsHealthy = True
```

### <a name="example-running-a-query"></a>Exemple : Exécution d’une requête

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

Et la sortie est:

|StartTime           |EndTime             |EpisodeId |EventId |State          |Type d’événement         |InjuriesDirect |BlessuresIndirect |DeathsDirect (en) |DeathsIndirect
|---------           |-------             |--------- |------- |-----          |---------         |-------------- |---------------- |------------ |--------------
|2007-12-30 16:00:00 |2007-12-30 16:05:00 |    11749 |  64588 |Géorgie        |Vent d’orage |             0 |               0 |           0 |             0
|2007-12-20 07:50:00 |2007-12-20 07:53:00 |    12554 |  68796 |Mississippi    |Vent d’orage |             0 |               0 |           0 |             0
|2007-09-29 08:11:00 |2007-09-29 08:11:00 |    11091 |  61032 |ATLANTIQUE SUD |Trombe d’eau        |             0 |               0 |           0 |             0
|2007-09-20 21:57:00 |2007-09-20 22:05:00 |    11078 |  60913 |Floride        |Tornade           |             0 |               0 |           0 |             0
|2007-09-18 20:00:00 |2007-09-19 18:00:00 |    11074 |  60904 |Floride        |Pluie abondante        |             0 |               0 |           0 |             0