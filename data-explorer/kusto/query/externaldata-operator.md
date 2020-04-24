---
title: opérateur de données externes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de données externes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 0a4a151d1fd052e27e4daf76539ef6dbd9d94c83
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515468"
---
# <a name="externaldata-operator"></a>externaldata, opérateur

L’opérateur `externaldata` retourne une table dont le schéma est défini dans la requête elle-même, et dont les données sont lues à partir d’un artefact de stockage externe (comme un blob dans Azure Blob Storage).

> [!NOTE]
> Cet exploitant n’a pas d’entrée de pipeline.

**Syntaxe**

`externaldata``(` *ColumnName* `:` *ColumnType* [...]`,` `)` `]` `with` `(` `=` *Value1* `,` *Prop1* *StorageConnectionString* StorageConnectionString [ Prop1 Value1 [ ...] `[` `)`]

**Arguments**

* *ColumnName*, *ColumnType*: Définir le schéma de la table.
  La syntaxe est la même que la syntaxe utilisée lors de la définition d’une table en [.créer table](../management/create-table-command.md).

* *StorageConnectionString*: La [chaîne de connexion de stockage](../api/connection-strings/storage.md) décrit l’artefact de stockage qui retient les données pour revenir.

* *Prop1*, *Value1*, ...: Propriétés supplémentaires qui décrivent comment interpréter les données récupérées à partir du stockage, comme énumérés sous [les propriétés d’ingestion](../management/data-ingestion/index.md).
    * Propriétés actuellement `format` `ignoreFirstRecord`prises en charge: et .
    * Formats de données pris en charge : tous les `csv` `tsv`formats de données `json` `parquet` `avro` [d’ingestion](https://docs.microsoft.com/azure/data-explorer/ingestion-supported-formats) sont pris en charge, y compris , , , . .

**Retourne**

L’exploitant `externaldata` retourne un tableau de données du schéma donné dont les données ont été analysées à partir de l’artefact de stockage spécifié indiqué par la chaîne de connexion de stockage.

**Exemple**

L’exemple suivant montre comment trouver tous `UserID` les enregistrements dans un tableau dont la colonne tombe dans un ensemble connu d’IDs, détenus (un par ligne) dans un blob externe.
Parce que l’ensemble est indirectement référencé par la requête, il peut être très grand.

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```