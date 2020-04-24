---
title: Joignez-vous à la diffusion - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Broadcast Join in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 72c89bf2160f8f5b735fd8c93f9519feae9114d9
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517304"
---
# <a name="broadcast-join"></a>Jonction de diffusion

Aujourd’hui, les jointures régulières sont exécutées sur un seul nœud de cluster.
L’adhésion à la diffusion est une stratégie d’exécution de l’adhésion qui le distribuera sur les nœuds cluster, et il est utile lorsque le côté gauche de la jointure est petit (jusqu’à 100K enregistrements), Dans ce cas, la jointure sera plus performant que la jointure régulière.

Si le côté gauche de la jointure est un petit jeu de données, alors vous pouvez exécuter rejoindre en mode de diffusion en utilisant la syntaxe suivante (hint.strategy - diffusion):

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on key
```

L’amélioration des performances sera plus perceptible dans les scénarios `summarize`où l’adhésion est suivie par d’autres opérateurs tels que . par exemple dans cette requête :

```kusto
lookupTable 
| join hint.strategy = broadcast (factTable) on Key
| summarize dcount(Messages) by Timestamp, Key
```