---
title: opérateur ExternalData-Azure Explorateur de données
description: Cet article décrit l’opérateur de données externes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: 4534705156669447a89cb5d85c360071dfcb2b2a
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264995"
---
# <a name="externaldata-operator"></a>externaldata, opérateur

L' `externaldata` opérateur retourne une table dont le schéma est défini dans la requête elle-même, et dont les données sont lues à partir d’un artefact de stockage externe, tel qu’un objet BLOB dans le stockage d’objets BLOB Azure.

**Syntaxe**

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...] `)` `[` *StorageConnectionString* `]` [ `with` `(` *Prop1* `=` *value1* [ `,` ...] `)` ]

**Arguments**

* *ColumnName*, *ColumnType*: les arguments définissent le schéma de la table.
  La syntaxe est la même que celle utilisée lors de la définition d’une table dans [. Create table](../management/create-table-command.md).

* *StorageConnectionString*: la [chaîne de connexion de stockage](../api/connection-strings/storage.md) décrit l’artefact de stockage contenant les données à retourner.

* *Prop1*, *valeur1*,... : propriétés supplémentaires qui décrivent comment interpréter les données récupérées à partir du stockage, comme indiqué sous [Propriétés](../../ingestion-properties.md)d’ingestion.
    * Propriétés actuellement prises en charge : `format` et `ignoreFirstRecord` .
    * Formats de données pris en charge : l’un des [formats de données](../../ingestion-supported-formats.md) d’ingestion est pris en charge, notamment,,, `CSV` `TSV` `JSON` `Parquet` , `Avro` .

> [!NOTE]
> Cet opérateur n’a pas d’entrée de pipeline.

**Retourne**

L' `externaldata` opérateur retourne une table de données du schéma donné dont les données ont été analysées à partir de l’artefact de stockage spécifié, indiqué par la chaîne de connexion de stockage.

**Exemples**

L’exemple suivant montre comment rechercher tous les enregistrements d’une table dont `UserID` la colonne appartient à un ensemble connu d’ID, détenu (un par ligne) dans un objet BLOB externe.
Étant donné que l’ensemble est référencé indirectement par la requête, il peut être volumineux.

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt"
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

L’exemple suivant interroge plusieurs fichiers de données stockés dans un stockage externe.

```kusto
externaldata(Timestamp:datetime, ProductId:string, ProductDescription:string)
[
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/01/part-00000-7e967c99-cf2b-4dbb-8c53-ce388389470d.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/02/part-00000-ba356fa4-f85f-430a-8b5a-afd64f128ca4.csv.gz?...SAS...",
  h@"https://mycompanystorage.blob.core.windows.net/archivedproducts/2019/01/03/part-00000-acb644dc-2fc6-467c-ab80-d1590b23fc31.csv.gz?...SAS..."
]
with(format="csv")
| summarize count() by ProductId
```

L’exemple ci-dessus peut être considéré comme un moyen rapide d’interroger plusieurs fichiers de données sans définir de [table externe](schema-entities/externaltables.md).

> [!NOTE]
> Le partitionnement des données n’est pas reconnu par l' `externaldata()` opérateur.
