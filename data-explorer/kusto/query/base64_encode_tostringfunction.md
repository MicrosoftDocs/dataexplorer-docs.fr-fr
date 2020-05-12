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
ms.openlocfilehash: 332ff6bedd268dd79be020ff1dc4d0591ed486f7
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225307"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Encode une chaîne au format de chaîne base64.

**Syntaxe**

`base64_encode_tostring(`*Chaîne*`)`

**Arguments**

* *Chaîne*: chaîne d’entrée à encoder sous la forme d’une chaîne base64.

**Retourne**

Retourne la chaîne encodée en base64 String.

* Pour décoder des chaînes Base64 dans une chaîne UTF-8, consultez [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Pour décoder des chaînes Base64 dans un tableau de valeurs longues, consultez [base64_decode_toarray ()](base64_decode_toarrayfunction.md)


**Exemple**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|
