---
title: base64_encode_tostring ()-Azure Explorateur de données
description: Cet article décrit base64_encode_tostring () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 6fca71cbc7cf33f49a9f4698553391a84ddbb728
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252667"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Encode une chaîne au format de chaîne base64.

## <a name="syntax"></a>Syntaxe

`base64_encode_tostring(`*Chaîne*`)`

## <a name="arguments"></a>Arguments

* *Chaîne*: chaîne d’entrée à encoder sous la forme d’une chaîne base64.

## <a name="returns"></a>Retours

Retourne la chaîne encodée en base64 String.

* Pour décoder des chaînes base64 en chaînes UTF-8, consultez [base64_decode_tostring ()](base64_decode_tostringfunction.md)
* Pour décoder des chaînes base64 en un tableau de valeurs longues, consultez [base64_decode_toarray ()](base64_decode_toarrayfunction.md)


## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|Quine   |
|--------|
|S3VzdG8 =|

