---
title: url_encode_component ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit url_encode_component () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 44e77e2a0e34b66506852bd82d65e4a108b520fd
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338454"
---
# <a name="url_encode_component"></a>url_encode_component()

La fonction convertit les caractères de l’URL d’entrée dans un format qui peut être transmis sur Internet. 

Vous trouverez des informations détaillées sur l’encodage et le décodage d’URL [ici](https://en.wikipedia.org/wiki/Percent-encoding).
Diffère de [url_encode](./urlencodefunction.md) en encodant des espaces comme « 20% » et non pas en tant que « + ».

## <a name="syntax"></a>Syntaxe

`url_encode_component(`*URL*`)`

## <a name="arguments"></a>Arguments

* *URL*: URL d’entrée (String).  

## <a name="returns"></a>Retours

URL (String) convertie dans un format qui peut être transmis sur Internet.

## <a name="examples"></a>Exemples

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|ressource d’origine|encodé|
|---|---|
|https://www.bing.com/helloautomatique|https %3 a %2 f %2 f www. Bing. com% 2fhello% 20word|


 