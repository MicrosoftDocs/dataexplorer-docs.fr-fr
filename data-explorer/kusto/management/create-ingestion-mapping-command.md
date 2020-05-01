---
title: . créer un mappage d’ingestion-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. créer un mappage d’ingestion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 84ab277f5b0d4d1b2e09d31fb7c1254786affe6d
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617729"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

Crée un mappage d’ingestion associé à une table spécifique et à un format spécifique.

**Syntaxe**

`.create``table` *TableName* TableName `ingestion` *MappingKind* MappingKind `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * Une fois créé, le mappage peut être référencé par son nom dans les commandes d’ingestion, au lieu de spécifier le mappage complet dans le cadre de la commande.
> * Les valeurs valides pour _MappingKind_ sont `JSON`: `avro` `CSV`, `parquet`,, et`orc`
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

| Nom     | Type | Mappage                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | [{« Name » : « RowNumber », « DataType » : « int », « CsvDataType » : null, « ordinal » : 0, « ConstValue » : null}, {« Name » : « rowguid », « DataType » : « String », « CsvDataType » : null, « ordinal » : 1, « ConstValue » : NULL}] |

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le mappage d’ingestion, consultez [mappages de données](mappings.md).
