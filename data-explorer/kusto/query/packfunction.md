---
title: Pack ()-Explorateur de données Azure | Microsoft Docs
description: Cet article décrit Pack () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a94629ae8f4795e28cbfb0c41f06596731cdd8d9
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717323"
---
# <a name="pack"></a>pack()

Crée un `dynamic` objet (conteneur de propriétés) à partir d’une liste de noms et de valeurs.

Alias pour `pack_dictionary()` fonctionner.

**Syntaxe**

`pack(`*key1* `,` *valeur1* `,` *Key2* `,` *valeur2*`,... )`

**Arguments**

* Une liste alternative de clés et de valeurs (la longueur totale de la liste doit être paire)
* Toutes les clés doivent être des chaînes constantes non vides

**Exemples**

L’exemple suivant retourne `{"Level":"Information","ProcessID":1234,"Data":{"url":"www.bing.com"}}`:

```kusto
pack("Level", "Information", "ProcessID", 1234, "Data", pack("url", "www.bing.com"))
```

Permet de prendre 2 tables, SmsMessages et MmsMessages :

Table SmsMessages 

|SourceNumber |TargetNumber| CharsCount
|---|---|---
|555-555-1234 |555-555-1212 | 46 
|555-555-1234 |555-555-1213 | 50 
|555-555-1212 |555-555-1234 | 32 

Table MmsMessages 

|SourceNumber |TargetNumber| Pièce jointe | AttachmentType | AttachmentName
|---|---|---|---|---
|555-555-1212 |555-555-1213 | 200 | jpeg | Pic1
|555-555-1234 |555-555-1212 | 250 | jpeg | Note2
|555-555-1234 |555-555-1213 | 300 | png | Pic3

Cette requête :
```kusto
SmsMessages 
| extend Packed=pack("CharsCount", CharsCount) 
| union withsource=TableName kind=inner 
( MmsMessages 
  | extend Packed=pack("AttachmentSize", AttachmentSize, "AttachmentType", AttachmentType, "AttachmentName", AttachmentName))
| where SourceNumber == "555-555-1234"
``` 

Retourne les informations suivantes :

|TableName |SourceNumber |TargetNumber | Riche
|---|---|---|---
|SmsMessages|555-555-1234 |555-555-1212 | {"CharsCount" : 46}
|SmsMessages|555-555-1234 |555-555-1213 | {"CharsCount" : 50}
|MmsMessages|555-555-1234 |555-555-1212 | {"Attachment" : 250, "AttachmentType" : "JPEG", "AttachmentName" : "Note2"}
|MmsMessages|555-555-1234 |555-555-1213 | {"Attachment" : 300, "AttachmentType" : "png", "AttachmentName" : "Pic3"}
