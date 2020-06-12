---
title: base64_encode_tostring ()-Azure Explorateur de données
description: Cet article décrit base64_encode_tostring () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: c414f1bdb83850bc6ec6065314bc7c8662ab0ed2
ms.sourcegitcommit: ae72164adc1dc8d91ef326e757376a96ee1b588d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84717085"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Encode une chaîne au format de chaîne base64.

**Syntaxe**

`base64_encode_tostring(`*Chaîne*`)`

**Arguments**

* *Chaîne*: chaîne d’entrée à encoder sous la forme d’une chaîne base64.

**Retourne**

Retourne la chaîne encodée en base64 String.

* Pour décoder des chaînes base64 en chaînes UTF-8, consultez [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Pour décoder des chaînes base64 en un tableau de valeurs longues, consultez [base64_decode_toarray ()](base64_decode_toarrayfunction.md)


**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|

