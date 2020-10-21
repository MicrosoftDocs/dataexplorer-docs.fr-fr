---
title: Pack ()-Explorateur de données Azure | Microsoft Docs
description: Cet article décrit Pack () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7b72a4ab3f64fb119d8a35767ea4e5cfedfdf71f
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248672"
---
# <a name="pack"></a>pack()

Crée un `dynamic` objet (conteneur de propriétés) à partir d’une liste de noms et de valeurs.

Alias pour `pack_dictionary()` fonctionner.

## <a name="syntax"></a>Syntaxe

`pack(`*key1* `,` *valeur1* `,` *Key2* `,` *valeur2*`,... )`

## <a name="arguments"></a>Arguments

* Une liste alternative de clés et de valeurs (la longueur totale de la liste doit être paire)
* Toutes les clés doivent être des chaînes constantes non vides

## <a name="examples"></a>Exemples

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
