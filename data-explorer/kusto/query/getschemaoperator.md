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
ms.openlocfilehash: 4fe19148ef8f410f04dc68f435734a2c2c425cca
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347678"
---
# <a name="getschema-operator"></a>opérateur getschema 

Produit une table qui représente un schéma tabulaire de l’entrée.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

## <a name="syntax"></a>Syntaxe

*T* `| ``getschema`

## <a name="example"></a>Exemple

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
