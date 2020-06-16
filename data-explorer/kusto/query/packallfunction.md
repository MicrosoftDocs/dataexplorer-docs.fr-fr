---
title: pack_all ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit pack_all () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f34f2ac316f122034fcf8ee19a8e82e51b6221df
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780471"
---
# <a name="pack_all"></a>pack_all()

Crée un `dynamic` objet (conteneur de propriétés) à partir de toutes les colonnes de l’expression tabulaire.

**Syntaxe**

`pack_all()`

**Notes**

La représentation de l’objet retourné n’est pas garantie d’être compatible au niveau de l’octet entre les exécutions. Par exemple, les propriétés qui apparaissent dans le conteneur peuvent apparaître dans un ordre différent.

**Exemples**

À partir d’une table SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Cette requête :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(SourceNumber:string,TargetNumber:string,CharsCount:long)
[
'555-555-1234','555-555-1212',46,
'555-555-1234','555-555-1213',50,
'555-555-1212','555-555-1234',32
]
| extend Packed=pack_all()
```
Retourne les informations suivantes :

|TableName |SourceNumber |TargetNumber | Riche
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | {"SourceNumber" : "555-555-1234", "TargetNumber" : "555-555-1212", "CharsCount" : 46}
|SmsMessages|555-555-1234 |555-555-1213 | {"SourceNumber" : "555-555-1234", "TargetNumber" : "555-555-1213", "CharsCount" : 50}
|SmsMessages|555-555-1212 |555-555-1234 | {"SourceNumber" : "555-555-1212", "TargetNumber" : "555-555-1234", "CharsCount" : 32}
