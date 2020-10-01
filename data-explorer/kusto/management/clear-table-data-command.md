---
title: . effacer les données de la table-Azure Explorateur de données
description: Cet article décrit la `.clear table data` commande dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: vrozov
ms.service: data-explorer
ms.topic: reference
ms.date: 10/01/2020
ms.openlocfilehash: 513544b8fb373f7242bd36b250790801b39061b2
ms.sourcegitcommit: 1618cbad18f92cf0cda85cb79a5cc1aa789a2db7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91615071"
---
# <a name="clear-table-data"></a>. effacer les données de la table

Efface les données d’une table existante, y compris les données d’ingestion de diffusion en continu.

`.clear``table` *TableName*`data` 

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md)

**Exemple** 

```kusto
.clear table LyricsAsTable data 
```
 
