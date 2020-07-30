---
title: url_decode ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit url_decode () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0019e318b90f9626d9e55a593f19526cdc7cc9c7
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350585"
---
# <a name="url_decode"></a>url_decode()

La fonction convertit l’URL encodée en représentation en URL régulière. 

Vous trouverez des informations détaillées sur le décodage et l’encodage d’URL [ici](https://en.wikipedia.org/wiki/Percent-encoding).

## <a name="syntax"></a>Syntaxe

`url_decode(`*URL encodée*`)`

## <a name="arguments"></a>Arguments

* *URL encodée*: URL encodée (String).  

## <a name="returns"></a>Retourne

URL (String) dans une représentation normale.

## <a name="examples"></a>Exemples

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|ressource d’origine|décodé|
|---|---|
|https %3 a %2 f %2 f www. Bing. com% 2F|https://www.bing.com/|



 