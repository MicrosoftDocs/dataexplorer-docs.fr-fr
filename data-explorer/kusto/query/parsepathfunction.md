---
title: parse_path ()-Azure Explorateur de données
description: Cet article décrit parse_path () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 16b80c86f526cb05514577359603e9e21de80064
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83224882"
---
# <a name="parse_path"></a>parse_path()

Analyse un chemin d’accès de fichier `string` et retourne un [`dynamic`](./scalar-data-types/dynamic.md) objet qui contient les parties suivantes du chemin d’accès : Scheme, RootPath, DirectoryPath, DirectoryName, filename, extension, AlternateDataStreamName.
Outre les chemins d’accès simples avec les deux types de barres obliques, prend en charge les chemins d’accès aux schémas (par exemple, « file://... »), les chemins d’accès partagés (par exemple, « \\ shareddrive\users... »), les chemins d’accès longs (par exemple, « \\ ? \c :...

**Syntaxe**

`parse_path(`*d*`)`

**Arguments**

* *path*: chaîne qui représente un chemin d’accès de fichier.

**Retourne**

Objet de type `dynamic` qui incluait les composants de chemin d’accès répertoriés ci-dessus.

**Exemple**

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
|C:\temp\file.txt|{« Scheme » : « », «RootPath » : « C : », « DirectoryPath » : « C : \\ temp », « DirectoryName » : « Temp », « filename » : « file. txt », « extension » : « txt », « AlternateDataStreamName » : «»}
|temp\file.txt|{« Scheme » : « », «RootPath » : « », «DirectoryPath » : « Temp », « DirectoryName » : « Temp », « filename » : « file. txt », « extension » : « txt », « AlternateDataStreamName » : «»}
|file://C:/temp/file.txt:some.exe|{« Scheme » : « file », « RootPath » : « C : », « DirectoryPath » : « C:/Temp », « DirectoryName » : « Temp », « filename » : « file. txt », « extension » : « txt », « AlternateDataStreamName » : « Some. exe »}
|\\shared\users\temp\file.txt.gz|{« Scheme » : « », «RootPath » : « », «DirectoryPath » : « \\ \\ \\utilisateurs partagés \\ temp "," DirectoryName " :" Temp "," filename " :" file. txt. gz "," extension " :" gz "," AlternateDataStreamName " :" "}
|/usr/lib/temp/file.txt|{« Scheme » : « », «RootPath » : « », «DirectoryPath » : « /usr/lib/Temp », « DirectoryName » : « Temp », « filename » : « file. txt », « extension » : « txt », « AlternateDataStreamName » : «»}
