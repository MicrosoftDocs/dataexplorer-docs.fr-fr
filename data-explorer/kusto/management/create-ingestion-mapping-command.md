---
title: .créer la cartographie de l’ingestion - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .créer la cartographie d’ingestion dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 10e656b074516ad8b0018e627d9904251aebbf10
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744499"
---
# <a name="create-ingestion-mapping"></a>.create ingestion mapping

Crée une cartographie d’ingestion qui est associée à une table spécifique et un format spécifique.

**Syntaxe**

`.create``table` *TableName* `ingestion` *MappingKind MappingName* `mapping` *MappingName* *MappingFormattedAsJson*

> [!NOTE]
> * Une fois créée, la cartographie peut être référencée par son nom dans les commandes d’ingestion, au lieu de spécifier la cartographie complète dans le cadre de la commande.
> * Les valeurs valables `CSV`pour `JSON` `avro` _MappingKind_ sont : , , , `parquet`et`orc`
> * Si une cartographie du même nom existe déjà pour la table :
>    * `.create`échouera
>    * `.create-or-alter`modifiera la cartographie existante
 
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
| cartographie1 | CSV  | ["Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0":ConstValue":null', "Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null |
