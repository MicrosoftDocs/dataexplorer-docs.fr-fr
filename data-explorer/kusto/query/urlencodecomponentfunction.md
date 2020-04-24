---
title: url_encode_component() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit url_encode_component() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: bfdb40f362aa680a68bd8871769eecb5a2da19e6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505064"
---
# <a name="url_encode_component"></a>url_encode_component()

La fonction convertit les caractères de l’URL d’entrée en un format qui peut être transmis sur Internet. 

Des informations détaillées sur le codage et le décodage des URL peuvent être trouvées [ici](https://en.wikipedia.org/wiki/Percent-encoding).
Diffère de [url_encode](./urlencodefunction.md) en codant les espaces comme «20%» et non pas comme «en».

**Syntaxe**

`url_encode_component(`*Url*`)`

**Arguments**

* *URL*: URL d’entrée (corde).  

**Retourne**

URL (corde) convertie en format qui peut être transmis sur Internet.

**Exemples**

```kusto
let url = @'https://www.bing.com/hello word/';
print original = url, encoded = url_encode_component(url)
```

|ressource d’origine|Encodé|
|---|---|
|https://www.bing.com/hellomot/|https%3a%2f%2fwww.bing.com%2fhello%20word|


 