---
title: GetSchema, opérateur-Azure Explorateur de données
description: Cet article décrit l’opérateur GetSchema dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6960a737b0a71a6b921a6a58750e48f5c3fb9da0
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247368"
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
|Affichages|3|System.Int64|long
|BytesDelivered|4|System.Int64|long
