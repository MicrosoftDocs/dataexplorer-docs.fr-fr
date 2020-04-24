---
title: opérateur de la gamme - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de gamme dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f7ec559a23e28568ce7abd8365cdc502ad05a9b0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510606"
---
# <a name="range-operator"></a>opérateur range

Génère une table de valeurs à une seule colonne.

Notez qu’elle ne comporte pas d’entrée de pipeline. 

**Syntaxe**

`range`*colonneName* `from` étape *d’arrêt* `step` *step* *de démarrage* `to`

**Arguments**

* *colonneName*: Le nom de la colonne unique dans le tableau de sortie.
* *début*: La plus petite valeur de la sortie.
* *arrêt*: La valeur la plus élevée générée dans la sortie (ou une limite sur la valeur la plus élevée, si *l’étape* dépasse cette valeur).
* *étape*: La différence entre deux valeurs consécutives. 

Les arguments doivent être des valeurs de type numérique, date ou durée. Ils ne peuvent pas faire référence aux colonnes d’une table. (Si vous voulez calculer la plage en fonction d’une table d’entrée, utilisez la fonction de portée, peut-être avec l’opérateur mv-expand.) 

**Retourne**

Une table avec une seule colonne appelée *columnName*, dont *les*valeurs commencent, *étape* *de départ* `+` , ... jusqu’à et jusqu’à *l’arrêt*.

**Exemple**  

Table des occurrences de minuit au cours des sept derniers jours. La fonction d’emplacement (floor) diminue à chaque fois au début de la journée.

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

```kusto
range Steps from 1 to 8 step 3
```

L’exemple suivant `range` montre comment l’opérateur peut être utilisé pour créer une petite table de dimension ad hoc qui est ensuite utilisée pour introduire des zéros où les données sources n’ont pas de valeurs.

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