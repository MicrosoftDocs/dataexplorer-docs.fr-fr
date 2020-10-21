---
title: Rand ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit Rand () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1206f72b3951601c105de450bc6923861ad011ae
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92241291"
---
# <a name="rand"></a>rand()

Retourne un nombre aléatoire.

```kusto
rand()
rand(1000)
```

## <a name="syntax"></a>Syntaxe

* `rand()` -retourne une valeur de type `real` avec une distribution uniforme dans la plage [0,0, 1,0).
* `rand(`*N* `)` -retourne une valeur de type `real` choisie avec une distribution uniforme à partir du jeu {0,0, 1,0,..., *N* -1}.