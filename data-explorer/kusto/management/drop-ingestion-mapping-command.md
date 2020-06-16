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
ms.openlocfilehash: 69ab4849d67d7cc7507bab075b0111fbd8ec95e7
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780607"
---
# <a name="drop-ingestion-mapping"></a>.drop ingestion mapping

Supprime le mappage d’ingestion de la base de données.
 
`.drop``table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName*   

**Exemple** 

```kusto
.drop table MyTable ingestion csv mapping "Mapping1" 

.drop table MyTable ingestion json mapping "Mapping1" 
```
