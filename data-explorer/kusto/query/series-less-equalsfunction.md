---
title: series_less_equals() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit series_less_equals() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: 2dc03b1f1e24aaef4a6a006d72aeb980753e871e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81508277"
---
# <a name="series_less_equals"></a>series_less_equals()

Calcule le fonctionnement logique de`<=`l’élément-sage moins ou égal () de deux entrées de série numérique.

**Syntaxe**

`series_less_equals (`*Série 1* `,` *Série2*`)`

**Arguments**

* *Série1, Série2*: Entrées de tableaux numériques à être élément-sage comparé. Tous les arguments doivent être des tableaux dynamiques. 

**Retourne**

Gamme dynamique de booleans contenant l’élément calculé-sage moins ou égale fonction logique de fonctionnement entre les deux entrées. Tout élément non numérique ou élément non existant (tableaux de `null` différentes tailles) donne une valeur d’élément.

**Exemple**

```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_less_equals_s2 = series_less_equals(s1, s2)
```

|s1|s2|s1_less_equals_s2|
|---|---|---|
|[1,2,4]|[4,2,1]|[vrai, vrai, faux]|

**Voir aussi**

Pour des comparaisons de statistiques de séries entières, voir :
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)