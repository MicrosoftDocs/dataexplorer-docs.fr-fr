---
title: Rand ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Rand () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 53fc7512c1a0fb2019526f48ade54fabbd351d05
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345927"
---
# <a name="rand"></a>rand()

Retourne un nombre aléatoire.

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>Syntaxe

* `rand()`-retourne une valeur de type `real` avec une distribution uniforme dans la plage [0,0, 1,0).
* `rand(`*N* `)` -retourne une valeur de type `real` choisie avec une distribution uniforme à partir du jeu {0,0, 1,0,..., *N* -1}.