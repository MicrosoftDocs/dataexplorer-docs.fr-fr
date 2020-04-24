---
title: l’opérateur getschema - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur getschema dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2faeee575f1af72cfad808253853ae96aba7a28f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514363"
---
# <a name="getschema-operator"></a>opérateur getschema 

Produire une table qui représente un schéma tabulaire de l’entrée.

```kusto
T | summarize MyCount=count() by Country | getschema 
```

**Syntaxe**

*T T* `| ``getschema`

**Exemple**

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
|BytesDévérisé|4|System.Int64|long