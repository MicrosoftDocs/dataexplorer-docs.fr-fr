---
title: opérateur de plage-Azure Explorateur de données
description: Cet article décrit l’opérateur de plage dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1eddf7fe1e9121a134e222f6a19b9ebeede3a762
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243142"
---
# <a name="range-operator"></a>opérateur range

Génère une table de valeurs à une seule colonne.

Notez qu’elle ne comporte pas d’entrée de pipeline. 

## <a name="syntax"></a>Syntaxe

`range`*ColumnName* `from` *Démarrer* `to` *arrêter* `step` *étape*

## <a name="arguments"></a>Arguments

* *ColumnName*: nom de la colonne unique dans la table de sortie.
* *Start*: valeur la plus petite dans la sortie.
* *Stop*: valeur la plus élevée générée dans la sortie (ou liée à la valeur la plus élevée, si *Step* avance sur cette valeur).
* *Step*: différence entre deux valeurs consécutives. 

Les arguments doivent être des valeurs de type numérique, date ou durée. Ils ne peuvent pas faire référence aux colonnes d’une table. (Si vous souhaitez calculer la plage basée sur une table d’entrée, utilisez la fonction Range, peut-être avec l’opérateur MV-Expand.) 

## <a name="returns"></a>Retours

Une table avec une seule colonne nommée *ColumnName*, dont les valeurs sont *Start*, *Start* `+` *Step*,... jusqu’à et jusqu’à l' *arrêt*.

## <a name="example"></a>Exemple  

Table des occurrences de minuit au cours des sept derniers jours. La fonction d’emplacement (floor) diminue à chaque fois au début de la journée.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range LastWeek from ago(7d) to now() step 1d
```

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|2015-12-06 09:10:04.627|
|...|
|2015-12-12 09:10:04.627|


Une table comportant une seule colonne nommée `Steps` dont le type est `long` et dont les valeurs sont `1`, `4` et `7`.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range Steps from 1 to 8 step 3
```

L’exemple suivant montre comment l' `range` opérateur peut être utilisé pour créer une petite table de dimension ad hoc qui est ensuite utilisée pour introduire des zéros là où les données sources n’ont pas de valeurs.

```kusto
range TIMESTAMP from ago(4h) to now() step 1m
| join kind=fullouter
  (Traces
      | where TIMESTAMP > ago(4h)
      | summarize Count=count() by bin(TIMESTAMP, 1m)
  ) on TIMESTAMP
| project Count=iff(isnull(Count), 0, Count), TIMESTAMP
| render timechart  
```
