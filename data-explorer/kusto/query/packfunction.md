---
title: pack() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit pack () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a7b43be96f272ab929434f10cac910bd4072e650
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511830"
---
# <a name="pack"></a>pack()

Crée `dynamic` un objet (sac de propriété) à partir d’une liste de noms et de valeurs.

Alias `pack_dictionary()` pour fonctionner.

**Syntaxe**

`pack(`*key1* `,` *valeur1* `,` *key2* `,` *valeur2*`,... )`

**Arguments**

* Une liste alternée de clés et de valeurs (la longueur totale de la liste doit être égale)
* Toutes les touches doivent être des cordes constantes non vides

**Exemples**

L’exemple suivant retourne `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`:

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

Prenons 2 tables, SmsMessages et MmsMessages :

Tableau SmsMessages 

|SourceNumber (en) |CibleR| CharsCount (en)
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Tableau MmsMessages 

|SourceNumber (en) |CibleR| AttachmnetSize | AttachmnetType (en) | AttachmnetName (en anglais seulement)
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | JPEG | Photo1
|555-555-1234 |555-555-1212 | 250 | JPEG | Pic2
|555-555-1234 |555-555-1213 | 300 | png | Pic3

Cette requête :
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmnetSize", AttachmnetSize, "AttachmnetType", AttachmnetType, "AttachmnetName", AttachmnetName))
| where SourceNumber == "555-555-1234"
``` 

Retourne les informations suivantes :

|TableName |SourceNumber (en) |CibleR | Emballé
|---|---|---|---
|SmsMessages SmsMessages|555-555-1234 |555-555-1212 | "CharsCount": 46 ans
|SmsMessages SmsMessages|555-555-1234 |555-555-1213 | "CharsCount": 50 ans
|MmsMessages|555-555-1234 |555-555-1212 | "AttachmnetSize": 250, "AttachmnetType": "jpeg", "AttachmnetName": "Pic2"
|MmsMessages|555-555-1234 |555-555-1213 | "AttachmnetSize": 300, "AttachmnetType": "png", "AttachmnetName": "Pic3"