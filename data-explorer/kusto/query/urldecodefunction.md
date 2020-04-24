---
title: url_decode() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit url_decode() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3ffa5052a2fc30387be118683ec1df6f34f7346f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505149"
---
# <a name="url_decode"></a>url_decode()

La fonction convertit l’URL codée en une représentation URL régulière. 

Des informations détaillées sur le décodage et l’encodage des URL peuvent être trouvées [ici](https://en.wikipedia.org/wiki/Percent-encoding).

**Syntaxe**

`url_decode(`*URL codée*`)`

**Arguments**

* *URL codée*: URL codée (corde).  

**Retourne**

URL (corde) dans une représentation régulière.

**Exemples**

```kusto
let url = @'https%3a%2f%2fwww.bing.com%2f';
print original = url, decoded = url_decode(url)
```

|ressource d’origine|Décodé|
|---|---|
|https%3a%2f%2fwww.bing.com%2f|https://www.bing.com/|



 