---
title: . afficher les mappages d’ingestion-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. afficher les mappages d’ingestion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 711861a07896b7bdc4cf57bebbf1cdd0e01d064a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617168"
---
# <a name="show-ingestion-mappings"></a>. afficher les mappages d’ingestion

Affichez les mappages d’ingestion (tout ou partie du nom spécifiés).

* `.show``table` *TableName* TableName `ingestion` *MappingKind*  `mappings`

* `.show``table` *TableName* TableName `ingestion` *MappingKind* MappingKind`mapping` *MappingName*   

Afficher tous les mappages d’ingestion à partir de tous les types de mappages :

* `.show``table` *TableName*`ingestion`  `mapping`
 
**Exemple** 
 
```kusto
.show table MyTable ingestion csv mapping "Mapping1" 

.show table MyTable ingestion csv mappings 

.show table MyTable ingestion mappings 
```

**Exemple de sortie**

| Nom     | Type | Mappage     |
|----------|------|-------------|
| mapping1 | CSV  | [{« Name » : « RowNumber », « DataType » : « int », « CsvDataType » : null, « ordinal » : 0, « ConstValue » : null}, {« Name » : « rowguid », « DataType » : « String », « CsvDataType » : null, « ordinal » : 1, « ConstValue » : NULL}] |
