---
title: zlib_compress_to_base64_string-Explorateur de données Azure
description: Cet article décrit la commande zlib_compress_to_base64_string () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 09/29/2020
ms.openlocfilehash: c43ffcbb449c6fc8a4c01b7a032df50c40829702
ms.sourcegitcommit: 463ee13337ed6d6b4f21eaf93cf58885d04bccaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573516"
---
# <a name="zlib_compress_to_base64_string"></a>zlib_compress_to_base64_string ()

Effectue une compression zlib et encode le résultat en base64.

> [!NOTE]
> La seule taille de Windows prise en charge est 15.

## <a name="syntax"></a>Syntaxe

`zlib_compress_to_base64_string('input_string')`

## <a name="arguments"></a>Arguments

*input_string*: entrée `string` , chaîne à compresser et encodée en base64. La fonction accepte un argument de chaîne.

## <a name="returns"></a>retourne :

* Retourne un `string` qui représente une chaîne d’origine encodée zlib et encodée en base64. 
* Retourne un résultat vide si la compression ou l’encodage a échoué.

## <a name="example"></a>Exemple

### <a name="using-kusto"></a>Utilisation de Kusto

```kusto
print zcomp = zlib_compress_to_base64_string("1234567890qwertyuiop")
```

**Sortie :** | " eAEBFADr/zEyMzQ1Njc4OTBxd2VydHl1aW9wOAkGdw = = "|

### <a name="using-python"></a>Utilisation de Python

La compression peut être effectuée à l’aide d’autres outils, par exemple Python : 

```python
print(base64.b64encode(zlib.compress(b'<original_string>')))
```

## <a name="next-steps"></a>Étapes suivantes

Utilisez [zlib_decompress_from_base64_string ()](zlib-base64-decompress.md) pour récupérer la chaîne non compressée d’origine.