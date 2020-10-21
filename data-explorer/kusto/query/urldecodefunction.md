---
title: url_decode ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit url_decode () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b58fea5d367cf31b495b23a09bc0a0dcb6bb95c6
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251857"
---
# <a name="url_decode"></a>url_decode()

La fonction convertit l’URL encodée en représentation en URL régulière. 

Vous trouverez des informations détaillées sur le décodage et l’encodage d’URL [ici](https://en.wikipedia.org/wiki/Percent-encoding).

## <a name="syntax"></a>Syntaxe

`url_decode(`*URL encodée*`)`

## <a name="arguments"></a>Arguments

* *URL encodée*: URL encodée (String).  

## <a name="returns"></a>Retours

URL (String) dans une représentation normale.

## <a name="examples"></a>Exemples

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|ressource d’origine|décodé|
|---|---|
|https %3 a %2 f %2 f www. Bing. com% 2F|https://www.bing.com/|



 