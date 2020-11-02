---
title: gzip_decompress_from_base64_string ()-Azure Explorateur de données
description: Cet article décrit la commande gzip_decompress_from_base64_string () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 11/01/2020
ms.openlocfilehash: 19fc8c7ce74cfe632034722fda2b23c5105d013a
ms.sourcegitcommit: 0e2fbc26738371489491a96924f25553a8050d51
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2020
ms.locfileid: "93148533"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string ()

Décode la chaîne d’entrée à partir de base64 et effectue une décompression gzip.

## <a name="syntax"></a>Syntax

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>Arguments

*input_string* : entrée `string` compressée avec gzip, puis encodée en base64. La fonction accepte un argument de chaîne.

## <a name="returns"></a>Retours

* Retourne un `string` qui représente la chaîne d’origine. 
* Retourne un résultat vide si la décompression ou le décodage a échoué. 
    * Par exemple, les chaînes gzip-Compressed et base 64 non valides renverront une sortie vide.

## <a name="examples"></a>Exemples

```kusto
print res=gzip_decompress_from_base64_string("eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGd0xvZwAzAG5JZA==")
```

**Output:**

|" 1234567890qwertyuiop "|

Exemple d’entrée non valide :

```kusto
print res=gzip_decompress_from_base64_string("x0x0x0")
```

**Output:**
>||

## <a name="next-steps"></a>Étapes suivantes

Créez une chaîne d’entrée compressée avec [gzip_compress_to_base64_string ()](gzip-base64-compress.md).
