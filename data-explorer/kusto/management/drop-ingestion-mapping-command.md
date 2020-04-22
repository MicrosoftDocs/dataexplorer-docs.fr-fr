---
title: Cartographie d’ingestion de .drop - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la cartographie de l’ingestion .drop dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: c1434234033acc73de35289c6bc0a90af727babb
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744768"
---
# <a name="drop-ingestion-mapping"></a>.drop ingestion mapping

Baisse la cartographie de l’ingestion de la base de données.
 
`.drop``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

**Exemple** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion JSON mappings "Mapping1" 
```
