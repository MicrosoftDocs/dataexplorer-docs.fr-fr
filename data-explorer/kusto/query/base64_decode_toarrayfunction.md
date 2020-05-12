---
title: base64_decode_toarray ()-Azure Explorateur de données
description: Cet article décrit base64_decode_toarray () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: eda367dfeaab15dc5249fd860596964c597a1bcd
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225409"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Décode une chaîne base64 en un tableau de valeurs longues.

**Syntaxe**

`base64_decode_toarray(`*Chaîne*`)`

**Arguments**

* *Chaîne*: chaîne d’entrée à décoder de la chaîne base64 à la chaîne UTF8-8.

**Retourne**

Retourne un tableau de valeurs longues ecoded à partir d’une chaîne base64.

* Pour décoder des chaînes Base64 dans une chaîne UTF-8, consultez [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Pour encoder des chaînes en chaîne base64 [, consultez base64_encode_tostring ()](base64_encode_tostringfunction.md)

**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quine|
|-----|
|[75 117 115 116 111]|

La tentative de décodage d’une chaîne base64 qui a été générée à partir d’un encodage UTF-8 non valide retourne la valeur NULL :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Vide|
|-----|
||
