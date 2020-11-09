---
title: gzip_compress_to_base64_string-Explorateur de données Azure
description: Cet article décrit la commande gzip_compress_to_base64_string () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: b2bed8fab33de57da6286a7ea49b9378669a9d5a
ms.sourcegitcommit: 4b061374c5b175262d256e82e3ff4c0cbb779a7b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2020
ms.locfileid: "94373848"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string()

Exécute la compression gzip et encode le résultat en base64.


## <a name="syntax"></a>Syntax

`gzip_compress_to_base64_string("`*input_string* »`)`

## <a name="arguments"></a>Arguments

*input_string* : entrée `string` , chaîne à compresser et encodée en base64. La fonction accepte un argument de chaîne.

## <a name="returns"></a>Retours

* Retourne un `string` qui représente une chaîne d’origine encodée avec gzip et encodée en base64. 
* Retourne un résultat vide si la compression ou l’encodage a échoué.

## <a name="example"></a> Exemple
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**Output:** 

| eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA = = |

## <a name="next-steps"></a>Étapes suivantes

Utilisez [gzip_decompress_from_base64_string ()](gzip-base64-decompress.md) pour récupérer la chaîne non compressée d’origine.
