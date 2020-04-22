---
title: parse_path() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit parse_path() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 2267efb4e47a6d8e45733ad48dd9f7f4019c1fa8
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744664"
---
# <a name="parse_path"></a>parse_path()

Parses un `string` chemin de [`dynamic`](./scalar-data-types/dynamic.md) fichier et renvoie un objet qui contient les parties suivantes du chemin: Scheme, RootPath, DirectoryPath, DirectoryName, FileName, Extension, AlternateDataStreamName.
En plus des chemins simples avec les deux types de barres obliques, soutient les chemins avec des\\schémas (par exemple "file://..."),\\les chemins partagés (par exemple "shareddrive-users..."), les longs chemins (par exemple " ? ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' ' '

**Syntaxe**

`parse_path(`*Chemin*`)`

**Arguments**

* *chemin*: Une chaîne qui représente un chemin de fichier.

**Retourne**

Un objet `dynamic` de type qui inculdait les composants du chemin énumérés ci-dessus.

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
|C : 'temp’file.txt|"Scheme":"","RootPath":"C:","DirectoryPath":"C:\\temp","DirectoryName":"temp","Filename":"file.txt","Extension":"txt","AlternateDataStreamName":"""
|temp-file.txt|"Scheme":"","RootPath":"","DirectoryPath":"temp","DirectoryName":"temp","Filename":"file.txt","Extension":"txt","AlternateDataStreamName":""MD
|file://C:/temp/file.txt:some.exe|"Scheme":"file","RootPath":"C:","DirectoryPath":"C:/temp","DirectoryName":"temp","Filename":"file.txt","Extension":"txt","AlternateDataStreamName":"some.exe"
|\\shared-users.temp-file.txt.gz|"Scheme":"","RootPath":"","DirectoryPath":"a\\\\\\partagé\\les utilisateurs temp","DirectoryName":"temp","Filename":"file.txt.gz","Extension":"gz","AlternateDataStreamName":"""
|/usr/lib/temp/file.txt|"Scheme":"","RootPath":"","DirectoryPath":"/usr/lib/temp","DirectoryName":"temp","Filename":"file.txt","Extension":"txt","AlternateDataStreamName":"""
