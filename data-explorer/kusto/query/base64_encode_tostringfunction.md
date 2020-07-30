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
ms.openlocfilehash: 218f87a3c11695c4aa8135f98b0c445580de9ad0
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349259"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

Encode une chaîne au format de chaîne base64.

## <a name="syntax"></a>Syntaxe

`base64_encode_tostring(`*Chaîne*`)`

## <a name="arguments"></a>Arguments

* *Chaîne*: chaîne d’entrée à encoder sous la forme d’une chaîne base64.

## <a name="returns"></a>Retourne

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

