---
title: . afficher les mappages d’ingestion-Azure Explorateur de données
description: Cet article décrit. afficher les mappages d’ingestion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 4a225c7d9cb1c5f99434c4595cbd798d020cfd9f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967432"
---
# <a name="show-ingestion-mapping"></a>.show ingestion mapping

Affichez les mappages d’ingestion (tout ou partie du nom spécifiés).

* `.show``table` *TableName* `ingestion` *MappingKind*  `mappings`

* `.show``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

Afficher tous les mappages d’ingestion à partir de tous les types de mappages :

* `.show` `table` *TableName* `ingestion`  `mapping`
 
**Exemple** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**Exemple de sortie**

| Nom     | Type | Mappage     |
|----------|------|-------------|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` |
