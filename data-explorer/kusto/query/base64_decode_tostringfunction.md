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
ms.openlocfilehash: d2e1c5dbb845e67a5306ccb16c234de383d3ca69
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225341"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Décode une chaîne base64 en une chaîne UTF-8.

**Syntaxe**

`base64_decode_tostring(`*Chaîne*`)`

**Arguments**

* *Chaîne*: chaîne d’entrée à décoder de la chaîne base64 à la chaîne UTF8-8.

**Retourne**

Retourne une chaîne UTF-8 décodée à partir d’une chaîne base64.

* Pour décoder des chaînes Base64 dans un tableau de valeurs longues, consultez [base64_decode_toarray ()](base64_decode_toarrayfunction.md)
* Pour encoder des chaînes en chaîne base64 [, consultez base64_encode_tostring ()](base64_encode_tostringfunction.md)

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Quine|
|-----|
|Kusto|

La tentative de décodage d’une chaîne base64 qui a été générée à partir d’un encodage UTF-8 non valide retourne la valeur NULL :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Vide|
|-----|
||
