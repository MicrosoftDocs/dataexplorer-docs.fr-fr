---
title: . suppression du mappage de réception-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le mappage des ingestions de dépôt dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 7454bd86a6ca2a835dc0515a9c8901a444259f12
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294523"
---
# <a name="drop-ingestion-mapping"></a>.drop ingestion mapping

Supprime le mappage d’ingestion de la base de données.
 
`.drop``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

**Exemple** 

```kusto
.drop table MyTable ingestion CSV mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
