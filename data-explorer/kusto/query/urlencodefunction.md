---
title: url_encode() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit url_encode () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 913be2d20af413f8ba89192f4db57e60fc6d7b27
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505047"
---
# <a name="url_encode"></a>url_encode()

La fonction convertit les caractères de l’URL d’entrée en un format qui peut être transmis sur Internet. 

Des informations détaillées sur le codage et le décodage des URL peuvent être trouvées [ici](https://en.wikipedia.org/wiki/Percent-encoding).
Diffère de [url_encode_component](./urlencodecomponentfunction.md) en codant les espaces comme «20%» et non pas comme «20%» (voir application/x-www-form-urlencoded [ici](https://en.wikipedia.org/wiki/Percent-encoding)).

**Syntaxe**

`url_encode(`*Url*`)`

**Arguments**

* *URL*: URL d’entrée (corde).  

**Retourne**

URL (corde) convertie en format qui peut être transmis sur Internet.

**Exemples**

```kusto
let url = @'https://www.bing.com/hello word';
print original = url, encoded = url_encode(url)
```

|ressource d’origine|Encodé|
|---|---|
|https://www.bing.com/hellomot/|https%3a%2f%2fwww.bing.com%2fhello-mot|


 