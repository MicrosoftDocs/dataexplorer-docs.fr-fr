---
title: opérateur ExternalData-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur ExternalData dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: d46a8669c523955f74d3f489c7b10e5b0f7ccef6
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373282"
---
# <a name="externaldata-operator"></a>externaldata, opérateur

L' `externaldata` opérateur retourne une table dont le schéma est défini dans la requête elle-même, et dont les données sont lues à partir d’un artefact de stockage externe (tel qu’un objet BLOB dans le stockage d’objets BLOB Azure).

> [!NOTE]
> Cet opérateur n’a pas d’entrée de pipeline.

**Syntaxe**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *value1* [ `,` ...] `)` ]

**Arguments**

* *ColumnName*, *ColumnType*: définit le schéma de la table.
  La syntaxe est la même que celle utilisée lors de la définition d’une table dans [. Create table](../management/create-table-command.md).

* *StorageConnectionString*: la [chaîne de connexion de stockage](../api/connection-strings/storage.md) décrit l’artefact de stockage contenant les données à retourner.

* *Prop1*, *valeur1*,... : propriétés supplémentaires qui décrivent comment interpréter les données récupérées à partir du stockage, comme indiqué sous [Propriétés](../management/data-ingestion/index.md)d’ingestion.
    * Propriétés actuellement prises en charge : `format` et `ignoreFirstRecord` .
    * Formats de données pris en charge : l’un des [formats de données](../../ingestion-supported-formats.md) d’ingestion est pris en charge, notamment,,, `csv` `tsv` `json` `parquet` , `avro` .

**Retourne**

L' `externaldata` opérateur retourne une table de données du schéma donné dont les données ont été analysées à partir de l’artefact de stockage spécifié indiqué par la chaîne de connexion de stockage.

**Exemple**

L’exemple suivant montre comment rechercher tous les enregistrements d’une table dont `UserID` la colonne appartient à un ensemble connu d’ID, détenu (un par ligne) dans un objet BLOB externe.
Étant donné que l’ensemble est référencé indirectement par la requête, il peut être très volumineux.

```
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```