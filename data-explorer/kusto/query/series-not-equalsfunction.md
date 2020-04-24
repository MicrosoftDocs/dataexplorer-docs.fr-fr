---
title: series_not_equals() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_not_equals() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ce9c695ececf1ac9f1fbe783ebe0fa35986f3d0f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508175"
---
# <a name="series_not_equals"></a>series_not_equals()

Calcule l’élément-sage pas`!=`égal ( ) fonctionnement logique de deux entrées de série numérique.

**Syntaxe**

`series_not_equals (`*Série 1* `,` *Série2*`)`

**Arguments**

* *Série1, Série2*: Entrées de tableaux numériques à être élément-sage comparé. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Gamme dynamique de booleans contenant l’élément calculé-sage pas égale fonctionnement logique entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de `null` différentes tailles) donne une valeur d’élément.

**Exemple**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_not_equals_s2 = series_not_equals(s1, s2)
```

|s1|s2|s1_not_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[vrai, faux,vrai]|

**Voir aussi**

Pour des comparaisons de statistiques de séries entières, voir :
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)