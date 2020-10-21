---
title: base64_decode_toarray ()-Azure Explorateur de données
description: Cet article décrit base64_decode_toarray () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 34a4601c35acb9c95e09e49d201b2e1cddaace8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252721"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Décode une chaîne base64 en un tableau de valeurs longues.

## <a name="syntax"></a>Syntaxe

`base64_decode_toarray(`*Chaîne*`)`

## <a name="arguments"></a>Arguments

* *Chaîne*: chaîne d’entrée à décoder de la chaîne base64 à UTF8.

## <a name="returns"></a>Retours

Retourne un tableau de valeurs de type long décodées à partir d’une chaîne base64.

* Pour décoder des chaînes base64 en une chaîne UTF-8, consultez [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Pour encoder des chaînes dans une chaîne base64, consultez [base64_encode_tostring ()](base64_encode_tostringfunction.md)

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|Quine|
|-----|
|[75 117 115 116 111]|

Si vous essayez de décoder une chaîne base64 qui a été générée à partir d’un encodage UTF-8 non valide, la valeur « null » est retournée :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
