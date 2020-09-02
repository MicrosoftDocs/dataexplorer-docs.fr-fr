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
ms.openlocfilehash: f86c952fdbfadd0b6ff4177ce7aa194019639b20
ms.sourcegitcommit: d54e4ebb611da2b30158720e14103e81a7daa5af
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89286422"
---
# <a name="externaldata-operator"></a>externaldata, opérateur

L' `externaldata` opérateur retourne une table dont le schéma est défini dans la requête elle-même, et dont les données sont lues à partir d’un artefact de stockage externe, tel qu’un objet BLOB dans le stockage d’objets BLOB Azure ou un fichier dans Azure Data Lake Storage.

## <a name="syntax"></a>Syntaxe

`externaldata``(` *ColumnName* `:` *ColumnType* [ `,` ...]`)`   
`[`*StorageConnectionString* [ `,` ...]`]`   
[ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ]

## <a name="arguments"></a>Arguments

* *ColumnName*, *ColumnType*: les arguments définissent le schéma de la table.
  La syntaxe est la même que celle utilisée lors de la définition d’une table dans [. Create table](../management/create-table-command.md).

* *StorageConnectionString*: [chaînes de connexion de stockage](../api/connection-strings/storage.md) qui décrivent les artefacts de stockage contenant les données à retourner.

* *PropertyName*, *PropertyValue*,... : propriétés supplémentaires qui décrivent comment interpréter les données récupérées à partir du stockage, comme indiqué sous [Propriétés](../../ingestion-properties.md)d’ingestion.

Les propriétés actuellement prises en charge sont les suivantes :

| Propriété         | Type     | Description       |
|------------------|----------|-------------------|
| `format`         | `string` | Format de données. S’il n’est pas spécifié, une tentative est faite pour détecter le format de données à partir de l’extension de fichier (par défaut, `CSV` ). Les formats de [données](../../ingestion-supported-formats.md) d’ingestion sont pris en charge. |
| `ignoreFirstRecord` | `bool` | Si la valeur est true, indique que le premier enregistrement de chaque fichier est ignoré. Cette propriété est utile lors de l’interrogation de fichiers CSV avec des en-têtes. |
| `ingestionMapping` | `string` | Valeur de chaîne qui indique comment mapper les données du fichier source aux colonnes réelles dans le jeu de résultats de l’opérateur. Consultez [mappages de données](../management/mappings.md). |


> [!NOTE]
> Cet opérateur n’accepte aucune entrée de pipeline.

## <a name="returns"></a>Retours

L' `externaldata` opérateur retourne une table de données du schéma donné dont les données ont été analysées à partir de l’artefact de stockage spécifié, indiqué par la chaîne de connexion de stockage.

## <a name="examples"></a>Exemples

**Extraire la liste des ID d’utilisateur stockés dans le stockage d’objets BLOB Azure**

L’exemple suivant montre comment rechercher tous les enregistrements d’une table dont `UserID` la colonne appartient à un ensemble connu d’ID, détenu (un par ligne) dans un fichier de stockage externe. Étant donné que le format des données n’est pas spécifié, le format de données détecté est `TXT` .

```kusto
Users
| where UserID in ((externaldata (UserID:string) [
    @"https://storageaccount.blob.core.windows.net/storagecontainer/users.txt" 
      h@"?...SAS..." // Secret token needed to access the blob
    ]))
| ...
```

**Interroger plusieurs fichiers de données**

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
> Le partitionnement des données n’est pas reconnu par l' `externaldata` opérateur.

**Interroger des formats de données hiérarchiques**

Pour interroger le format de données hiérarchiques, par exemple,,, `JSON` `Parquet` `Avro` ou `ORC` , `ingestionMapping` doit être spécifié dans les propriétés de l’opérateur. Dans cet exemple, un fichier JSON est stocké dans le stockage d’objets BLOB Azure avec le contenu suivant :

```JSON
{
  "timestamp": "2019-01-01 10:00:00.238521",   
  "data": {    
    "tenant": "e1ef54a6-c6f2-4389-836e-d289b37bcfe0",   
    "method": "RefreshTableMetadata"   
  }   
}   
{
  "timestamp": "2019-01-01 10:00:01.845423",   
  "data": {   
    "tenant": "9b49d0d7-b3e6-4467-bb35-fa420a25d324",   
    "method": "GetFileList"   
  }   
}
...
```

Pour interroger ce fichier à l’aide de l' `externaldata` opérateur, vous devez spécifier un mappage de données. Le mappage détermine la façon de mapper les champs JSON aux colonnes de l’ensemble de résultats de l’opérateur :

```kusto
externaldata(Timestamp: datetime, TenantId: guid, MethodName: string)
[ 
   h@'https://mycompanystorage.blob.core.windows.net/events/2020/09/01/part-0000046c049c1-86e2-4e74-8583-506bda10cca8.json?...SAS...'
]
with(format='multijson', ingestionMapping='[{"Column":"Timestamp","Properties":{"Path":"$.time"}},{"Column":"TenantId","Properties":{"Path":"$.data.tenant"}},{"Column":"MethodName","Properties":{"Path":"$.data.method"}}]')
```

Le `MultiJSON` format est utilisé ici, car les enregistrements JSON uniques sont répartis sur plusieurs lignes.

Pour plus d’informations sur la syntaxe de mappage, consultez [Mappages de données](../management/mappings.md).
