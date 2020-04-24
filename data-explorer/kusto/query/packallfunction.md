---
title: pack_all() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit pack_all() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3c6a22b656e28b8b7113864e0b3f9636a4fb364d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511932"
---
# <a name="pack_all"></a>pack_all()

Crée `dynamic` un objet (sac de propriété) à partir de toutes les colonnes de l’expression tabulaire.

**Syntaxe**

`pack_all()`

**Exemples**

Compte tenu d’un tableau SmsMessages 

|SourceNumber (en) |CibleR| CharsCount (en)
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Cette requête :
```kusto
SmsMessages | extend Packed=pack_all()
``` 

Retourne les informations suivantes :

|TableName |SourceNumber (en) |CibleR | Emballé
|---|---|---|---
|SmsMessages SmsMessages|555-555-1234 |555-555-1212 | "SourceNumber":"555-555-1234", "TargetNumber":"555-555-1212", "CharsCount": 46
|SmsMessages SmsMessages|555-555-1234 |555-555-1213 | "SourceNumber":"555-555-1234", "TargetNumber":"555-555-1213", "CharsCount": 50
|SmsMessages SmsMessages|555-555-1212 |555-555-1234 | "SourceNumber":"555-555-1212", "TargetNumber":"555-555-1234", "CharsCount": 32