---
title: . modification du mappage d’ingestion-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le mappage de l’ingestion des modifications dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2f43039ff3935edbb6e92627d2f96b1c411e1ffa
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617814"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

Modifie un mappage d’ingestion existant associé à une table spécifique et un format spécifique (mappage complet remplacé).

**Syntaxe**

`.alter``table` *TableName* TableName `ingestion` *MappingKind* MappingKind `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * Ce mappage peut être référencé par son nom par les commandes d’ingestion, au lieu de spécifier le mappage complet dans le cadre de la commande.
> * Les valeurs valides pour _MappingKind_ sont `JSON`: `avro` `CSV`, `parquet`,, `orc`et.

**Exemple** 
 
```kusto
.alter table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"} }'
']'

.alter table MyTable ingestion json mapping "Mapping1"
'['
'   { "column" : "rownumber", "Properties":{"Path":"$.rownumber"}},'
'   { "column" : "rowguid", "Properties":{"Path":"$.rowguid"}}'
']'
```

**Exemple de sortie**

| Nom     | Type | Mappage                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | [{« Name » : « RowNumber », « DataType » : « int », « CsvDataType » : null, « ordinal » : 0, « ConstValue » : null}, {« Name » : « rowguid », « DataType » : « String », « CsvDataType » : null, « ordinal » : 1, « ConstValue » : NULL}] |
