---
title: base64_encode_fromarray ()-Azure Explorateur de données
description: Cet article décrit base64_encode_fromarray () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: ede35223b9cc57fddc5318659e6902130a80c011
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349276"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

Encode une chaîne base64 à partir d’un tableau d’octets.

## <a name="syntax"></a>Syntaxe

`base64_encode_fromarray(`*BytesArray*`)`

## <a name="arguments"></a>Arguments

* *BytesArray*: tableau d’octets d’entrée à encoder en chaîne base64.

## <a name="returns"></a>Retourne

Retourne la chaîne base64 encodée à partir du tableau d’octets.

* Pour décoder des chaînes Base64 dans une chaîne UTF-8, consultez [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Pour encoder des chaînes en chaîne base64 [, consultez base64_encode_tostring ()](base64_encode_tostringfunction.md)
* Cette fonction est l’inverse de [base64_decode_toarray ()](base64_decode_toarrayfunction.md)

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8 =|


Si vous tentez d’encoder une chaîne base64 à partir d’un tableau d’octets non valides généré à partir d’une chaîne UTF-8 non valide, la valeur null est retournée :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||
