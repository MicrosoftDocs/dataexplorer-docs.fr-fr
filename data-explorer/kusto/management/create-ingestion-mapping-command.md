---
title: . créer un mappage d’ingestion-Azure Explorateur de données
description: Cet article décrit. créer un mappage d’ingestion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3855d56ad31bbf98a6a075feb44a598b3bdbf52a
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329059"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

Crée un mappage d’ingestion associé à une table spécifique et à un format spécifique.

**Syntaxe**

`.create``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * Une fois créé, le mappage peut être référencé par son nom dans les commandes d’ingestion, au lieu de spécifier le mappage complet dans le cadre de la commande.
> * Les valeurs valides pour _MappingKind_ sont : `CSV` , `JSON` ,, `avro` `parquet` et`orc`
> * Si un mappage portant le même nom existe déjà pour la table :
>    * `.create`échoue
>    * `.create-or-alter`va modifier le mappage existant
 
**Exemple** 
 
```kusto
.create table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"}}'
']'

.create-or-alter table MyTable ingestion json mapping "Mapping1"
'['
'    { "column" : "rownumber", "datatype" : "int", "Properties":{"Path":"$.rownumber"}},'
'    { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**Exemple de sortie**

| Nom     | Genre | Mappage                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le mappage d’ingestion, consultez [mappages de données](mappings.md).
