---
title: series_less() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_less() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 8fc09141a6e60489ff2be246e145876357141785
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508260"
---
# <a name="series_less"></a>series_less()

Calcule le fonctionnement logique`<`de l’élément-sage moins () de deux entrées numériques.

**Syntaxe**

`series_less (`*Série 1* `,` *Série2*`)`

**Arguments**

* *Série1, Série2*: Entrées de tableaux numériques à être élément-sage comparé. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Gamme dynamique de booleans contenant l’élément calculé-sage moins fonctionnement logique entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de `null` différentes tailles) donne une valeur d’élément.

**Exemple**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_s2 = series_less(s1, s2)
```

|s1|s2|s1_less_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[vrai, faux, faux]|

**Voir aussi**

Pour des comparaisons de statistiques de séries entières, voir :
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)