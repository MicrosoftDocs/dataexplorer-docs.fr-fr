---
title: . modification du mappage d’ingestion-Azure Explorateur de données
description: Cet article décrit le mappage de l’ingestion des modifications dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: f62692c7f5a1b557038f452f5ed3c023ec9849f9
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84329025"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

Modifie un mappage d’ingestion existant associé à une table spécifique et un format spécifique (mappage complet remplacé).

**Syntaxe**

`.alter``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * Ce mappage peut être référencé par son nom par les commandes d’ingestion, au lieu de spécifier le mappage complet dans le cadre de la commande.
> * Les valeurs valides pour _MappingKind_ sont : `CSV` , `JSON` ,, `avro` `parquet` et `orc` .

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

| Nom     | Genre | Mappage                                                                                                                                                                          |
|----------|------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
