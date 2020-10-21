---
title: base64_decode_tostring ()-Azure Explorateur de données
description: Cet article décrit base64_decode_tostring () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: eab1065713bcbc73913a2ab17a99894d9af9f8a4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252711"
---
# <a name="base64_decode_tostring"></a>base64_decode_tostring()

Décode une chaîne base64 en une chaîne UTF-8.

## <a name="syntax"></a>Syntaxe

`base64_decode_tostring(`*Chaîne*`)`

## <a name="arguments"></a>Arguments

* *Chaîne*: chaîne d’entrée à décoder de la chaîne base64 à la chaîne UTF8-8.

## <a name="returns"></a>Retours

Retourne une chaîne UTF-8 décodée à partir d’une chaîne base64.

* Pour décoder des chaînes base64 en un tableau de valeurs longues, consultez [base64_decode_toarray ()](base64_decode_toarrayfunction.md)
* Pour décoder des chaînes en chaîne base64, consultez [base64_encode_tostring ()](base64_encode_tostringfunction.md)

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_tostring("S3VzdG8=")
```

|Quine|
|-----|
|Kusto|

Si vous tentez de décoder une chaîne base64 générée à partir d’un encodage UTF-8 non valide, la valeur null est retournée :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_tostring("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
