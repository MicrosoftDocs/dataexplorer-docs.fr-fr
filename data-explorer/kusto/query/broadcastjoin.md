---
title: Jonction de diffusion-Azure Explorateur de données
description: Cet article décrit la jonction de diffusion dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7987f44437d673b90954b674e63b70ac35e2432c
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128680"
---
# <a name="broadcast-join"></a>Jonction de diffusion

Aujourd’hui, les jointures régulières sont exécutées sur un seul nœud de cluster.
La jonction de diffusion est une stratégie d’exécution de Join, qui la distribue sur des nœuds de cluster. Cette stratégie est utile lorsque le côté gauche de la jointure est petit (jusqu’à 100 K enregistrements). Dans ce cas, la jointure de diffusion sera plus performante que la jointure normale.

Si le côté gauche de la jointure est un petit jeu de données, vous pouvez exécuter Join en mode de diffusion à l’aide de la syntaxe suivante (hint. Strategy = Broadcast) :

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

L’amélioration des performances sera plus perceptible dans les scénarios où la jointure est suivie par d’autres opérateurs tels que `summarize` . par exemple, dans cette requête :

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```