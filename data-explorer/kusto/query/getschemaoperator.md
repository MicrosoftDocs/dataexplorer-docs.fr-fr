---
title: GetSchema, opérateur-Azure Explorateur de données
description: Cet article décrit l’opérateur GetSchema dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 333c4d59a7ed62fd031ab52019c10abd821fd858
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226752"
---
# <a name="getschema-operator"></a>opérateur getschema 

Produit une table qui représente un schéma tabulaire de l’entrée.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**Syntaxe**

*T* `| ``getschema`

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| top 10 by Timestamp
| getschema
```

|ColumnName|ColumnOrdinal|DataType|ColumnType|
|---|---|---|---|
|Timestamp|0|System.DateTime|DATETIME|
|Langage|1|System.String|string|
|Page|2|System.String|string|
|Les vues|3|System.Int64|long
|BytesDelivered|4|System.Int64|long
