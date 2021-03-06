---
title: parse_path ()-Azure Explorateur de données
description: Cet article décrit parse_path () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 07224c01e1bd226575aff6555ca7cd0a82c049b2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251669"
---
# <a name="parse_path"></a>parse_path()

Analyse un chemin d’accès de fichier `string` et retourne un [`dynamic`](./scalar-data-types/dynamic.md) objet qui contient les parties suivantes du chemin d’accès :
* Schéma
* RootPath
* DirectoryPath
* DirectoryName
* FileName
* Extension
* AlternateDataStreamName

Outre les chemins d’accès simples avec les deux types de barres obliques, la fonction prend en charge les chemins d’accès avec :
* Schémas : Par exemple, « file://... »
* Chemins d’accès partagés. Par exemple, « \\ shareddrive\users... »
* Chemins d’accès longs. Par exemple, « \\ ? \c :... »
* Autres flux de données. Par exemple, « file1.exe:file2.exe »

## <a name="syntax"></a>Syntaxe

`parse_path(`*path*`)`

## <a name="arguments"></a>Arguments

* *path*: chaîne qui représente un chemin d’accès de fichier.

## <a name="returns"></a>Retours

Objet de type `dynamic` qui incluait les composants de chemin d’accès répertoriés ci-dessus.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(p:string) 
[
    @"C:\temp\file.txt",
    @"temp\file.txt",
    "file://C:/temp/file.txt:some.exe",
    @"\\shared\users\temp\file.txt.gz",
    "/usr/lib/temp/file.txt"
]
| extend path_parts = parse_path(p)

```

|p|path_parts
|---|---
|C:\temp\file.txt|{« Scheme » : « », «RootPath » : « C : », « DirectoryPath » : « C : \\ temp », « DirectoryName » : « Temp », « filename » : « file.txt », « extension » : « txt », « AlternateDataStreamName » : «»}
|temp\file.txt|{« Scheme » : « », «RootPath » : « », «DirectoryPath » : « Temp », « DirectoryName » : « Temp », « filename » : « file.txt », « extension » : « txt », « AlternateDataStreamName » : «»}
|file://C:/temp/file.txt:some.exe|{« Scheme » : « file », « RootPath » : « C : », « DirectoryPath » : « C:/Temp », « DirectoryName » : « Temp », « filename » : « file.txt », « extension » : « txt », « AlternateDataStreamName » : « some.exe »}
|\\shared\users\temp\file.txt. gz|{« Scheme » : « », «RootPath » : « », «DirectoryPath » : « \\ \\ \\utilisateurs partagés \\ temp "," DirectoryName " :" Temp "," filename " :" file.txt. gz "," extension " :" gz "," AlternateDataStreamName " :" "}
|/usr/lib/Temp/file.txt|{« Scheme » : « », «RootPath » : « », «DirectoryPath » : « /usr/lib/Temp », « DirectoryName » : « Temp », « filename » : « file.txt », « extension » : « txt », « AlternateDataStreamName » : «»}
