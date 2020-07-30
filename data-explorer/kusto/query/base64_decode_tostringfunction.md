---
title: base64_decode_tostring ()-Azure Explorateur de données
description: Cet article décrit base64_decode_tostring () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 17d88c8be518b6d31b67a327a7a5d42818132cb9
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349293"
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

|Vide|
|-----|
||
