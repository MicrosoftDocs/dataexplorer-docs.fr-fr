---
title: .alter cartographie d’ingestion - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .alter cartographie d’ingestion dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5343d55fadafce552c5d837e5eb50763ccf45a4c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522404"
---
# <a name="alter-ingestion-mapping"></a>.alter ingestion mapping

Modifie une cartographie d’ingestion existante qui est associée à une table spécifique et à un format spécifique (remplacement complet de la cartographie).

**Syntaxe**

`.alter``table` *TableName* `ingestion` *MappingKind MappingName* `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * Cette cartographie peut être référencée par son nom par des commandes d’ingestion, au lieu de spécifier la cartographie complète dans le cadre de la commande.
> * Les valeurs valides `CSV`pour `JSON` `avro` _MappingKind_ `orc`sont: , , , `parquet`et .

**Exemple** 
 
```
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
| cartographie1 | CSV  | ["Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0":ConstValue":null', "Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null |