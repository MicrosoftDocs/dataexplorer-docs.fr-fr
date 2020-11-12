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
ms.openlocfilehash: a35fd4ed2f43c991aa08e2e1594103cb5f5bd7a7
ms.sourcegitcommit: 3eabd78305d32cd9b8a6bd1d76877ddc19d8ac63
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548884"
---
# <a name="gzip_decompress_from_base64_string"></a>gzip_decompress_from_base64_string()

Décode la chaîne d’entrée à partir de base64 et effectue une décompression gzip.

## <a name="syntax"></a>Syntaxe

`gzip_decompress_from_base64_string("`*input_string*`")`

## <a name="arguments"></a>Arguments

*input_string* : entrée `string` compressée avec gzip, puis encodée en base64. La fonction accepte un argument de chaîne.

> [!NOTE]
> Cette fonction vérifie les champs d’en-tête gzip obligatoires (ID1, ID2 et CM) et retourne une sortie vide si l’un de ces champs a des valeurs incorrectes.
> Les champs d’en-tête facultatifs ne sont pas pris en charge. FLG et XFL sont supposés être des zéros.


## <a name="returns"></a>Retours

* Retourne un `string` qui représente la chaîne d’origine. 
* Retourne un résultat vide si la décompression ou le décodage a échoué. 
    * Par exemple, les chaînes gzip-Compressed et base 64 non valides renverront une sortie vide.

## <a name="examples"></a>Exemples

```kusto
print res=gzip_decompress_from_base64_string("H4sIAAAAAAAA/wEUAOv/MTIzNDU2Nzg5MHF3ZXJ0eXVpb3A6m7f2FAAAAA==")
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

* Créez une chaîne d’entrée compressée avec [gzip_compress_to_base64_string ()](gzip-base64-compress.md).
* Voir aussi [zlib_decompress_from_base64_string](zlib-base64-decompress.md).
