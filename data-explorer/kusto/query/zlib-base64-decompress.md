---
title: zlib_decompress_from_base64_string ()-Azure Explorateur de données
description: Cet article décrit la commande zlib_decompress_from_base64_string () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: e2163b3fe5460a660579889a589acd1e5fd965cd
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573511"
---
# <a name="zlib_decompress_from_base64_string"></a>zlib_decompress_from_base64_string ()

Décode la chaîne d’entrée à partir de base64 et effectue une décompression zlib.

> [!NOTE]
> La seule taille de Windows prise en charge est 15.

## <a name="syntax"></a>Syntaxe

`zlib_decompress_from_base64_string('input_string')`

## <a name="arguments"></a>Arguments

*input_string*: entrée `string` compressée avec zlib, puis encodée en base64. La fonction accepte un argument de chaîne.

## <a name="returns"></a>retourne :

* Retourne un `string` qui représente la chaîne d’origine. 
* Retourne un résultat vide si la décompression ou le décodage a échoué. 
    * Par exemple, les chaînes zlib-Compressed et base 64 non valides retournent une sortie vide.

## <a name="examples"></a>Exemples

```kusto
print zcomp = zlib_decompress_from_base64_string("eJwLSS0uUSguKcrMS1cwNDIGACxqBQ4=")
```

**Output:**

| Chaîne de test 123 |

Exemple d’entrée non valide :

```kusto
print zcomp = zlib_decompress_from_base64_string("x0x0x0")
```

**Sortie**
||

## <a name="next-steps"></a>Étapes suivantes

Créez une chaîne d’entrée compressée avec [zlib_compress_to_base64_string ()](zlib-base64-compress.md).