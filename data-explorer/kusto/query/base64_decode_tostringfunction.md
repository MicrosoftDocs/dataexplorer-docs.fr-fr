---
title: base64_decode_tostring ()-Azure Explorateur de données
description: Cet article décrit base64_decode_tostring () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: ffef2fd609075b0d5e5af5c4064e079c27cd8c94
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818502"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Décode une chaîne base64 en une chaîne UTF-8.

**Syntaxe**

`base64_decode_tostring(`*Chaîne*`)`

**Arguments**

* *Chaîne*: chaîne d’entrée à décoder de la chaîne base64 à la chaîne UTF8-8.

**Retourne**

Retourne une chaîne UTF-8 décodée à partir d’une chaîne base64.

* Pour décoder des chaînes base64 en un tableau de valeurs longues, consultez [base64_decode_toarray ()](base64_decode_toarrayfunction.md)
* Pour décoder des chaînes en chaîne base64, consultez [base64_encode_tostring ()](base64_encode_tostringfunction.md)

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Quine|
|-----|
|Kusto|

Si vous tentez de décoder une chaîne base64 générée à partir d’un encodage UTF-8 non valide, la valeur null est retournée :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Vide|
|-----|
||
