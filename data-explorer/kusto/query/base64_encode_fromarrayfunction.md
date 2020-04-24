---
title: base64_encode_fromarray() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit base64_encode_fromarray() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: f601463fd6751be7064892e70e5b2235f96a20ff
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518069"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

Encode une chaîne base64 à partir d’un tableau d’octets.

**Syntaxe**

`base64_encode_fromarray(`*BytesArray*`)`

**Arguments**

* *BytesArray*: Tableau d’octets d’entrée à coder en chaîne base64.

**Retourne**

Retourne la chaîne base64 codée à partir du tableau des octets.

* Pour décoder les cordes de base64 à une chaîne UTF-8 voir [base64_decode_tostring()](base64_decode_tostringfunction.md)
* Pour l’encodage des chaînes à base64 chaîne voir [base64_encode_tostring()](base64_encode_tostringfunction.md)
* Cette fonction est l’inverse de [base64_decode_toarray()](base64_decode_toarrayfunction.md)

**Exemple**

```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8|


Essayer d’encoder une chaîne base64 à partir d’un tableau d’octets invalide qui a été généré à partir de la chaîne codée UTF-8 invalide reviendra nulle :

```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||