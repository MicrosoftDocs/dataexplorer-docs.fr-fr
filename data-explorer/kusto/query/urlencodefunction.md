---
title: url_encode ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit url_encode () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 8ccc93286073003bdaf8324611888d32f60910fb
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350568"
---
# <a name="url_encode"></a>url_encode()

La fonction convertit les caractères de l’URL d’entrée dans un format qui peut être transmis sur Internet. 

Vous trouverez des informations détaillées sur l’encodage et le décodage d’URL [ici](https://en.wikipedia.org/wiki/Percent-encoding).
Diffère de [url_encode_component](./urlencodecomponentfunction.md) en encodant les espaces en tant que « + » et non pas en tant que « 20% » (voir application/x-www-form-urlencoded [ici](https://en.wikipedia.org/wiki/Percent-encoding)).

## <a name="syntax"></a>Syntaxe

`url_encode(`*URL*`)`

## <a name="arguments"></a>Arguments

* *URL*: URL d’entrée (String).  

## <a name="returns"></a>Retourne

URL (String) convertie dans un format qui peut être transmis sur Internet.

## <a name="examples"></a>Exemples

```kusto
let url = @'https://www.bing.com/hello word';
print original = url, encoded = url_encode(url)
```

|ressource d’origine|encodé|
|---|---|
|https://www.bing.com/helloautomatique|https %3 a %2 f %2 f www. Bing. com% 2fhello + Word|


 