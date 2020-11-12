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
ms.openlocfilehash: fffa39ca5fa41c065971b4feebfe60752b84ed59
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548867"
---
# <a name="gzip_compress_to_base64_string"></a>gzip_compress_to_base64_string()

Exécute la compression gzip et encode le résultat en base64.


## <a name="syntax"></a>Syntaxe

`gzip_compress_to_base64_string("`*input_string* »`)`

## <a name="arguments"></a>Arguments

*input_string* : entrée `string` , chaîne à compresser et encodée en base64. La fonction accepte un argument de chaîne.

## <a name="returns"></a>Retours

* Retourne un `string` qui représente une chaîne d’origine encodée avec gzip et encodée en base64. 
* Retourne un résultat vide si la compression ou l’encodage a échoué.

## <a name="example"></a>Exemple
```kusto
print res = gzip_compress_to_base64_string("1234567890qwertyuiop")
```

**Output:** 

| H4sIAAAAAAAA/wEUAOv/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA = = |

## <a name="next-steps"></a>Étapes suivantes

* Utilisez [gzip_decompress_from_base64_string ()](gzip-base64-decompress.md) pour récupérer la chaîne non compressée d’origine.
* Voir aussi [zlib_compress_to_base64_string ()](zlib-base64-compress.md)
