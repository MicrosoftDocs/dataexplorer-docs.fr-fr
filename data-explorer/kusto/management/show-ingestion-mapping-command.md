---
title: .show cartographies d’ingestion - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .show cartographies d’ingestion dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 91990fe40664ae89d69357812b0def2c7288eb7d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519820"
---
# <a name="show-ingestion-mappings"></a>.montrer les cartographies d’ingestion

Afficher les cartes d’ingestion (tout ou celle spécifiée par leur nom).

* `.show``table` *TableName* `ingestion` *MappingKind*  `mappings`

* `.show``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

Afficher toutes les cartes d’ingestion de toutes sortes de cartographies :

* `.show``table` *Nom de la table*`ingestion`  `mapping`
 
**Exemple** 
 
```
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**Exemple de sortie**

| Nom     | Type | Mappage     |
|----------|------|-------------|
| cartographie1 | CSV  | ["Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0":ConstValue":null', "Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null |