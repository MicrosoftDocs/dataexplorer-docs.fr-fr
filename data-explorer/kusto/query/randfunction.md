---
title: rand() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit rand() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d3638d4b979b7318f58efec0bed0da4c31896a9d
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510640"
---
# <a name="rand"></a>rand()

Retourne un nombre aléatoire.

```kusto
rand()
rand(1000)
```

**Syntaxe**

* `rand()`- renvoie une `real` valeur de type avec une distribution uniforme dans la gamme [0.0, 1.0).
* `rand(`*N* `)` - renvoie `real` une valeur de type choisie avec une distribution uniforme de l’ensemble 0,0, 1,0, ..., *N* - 1.