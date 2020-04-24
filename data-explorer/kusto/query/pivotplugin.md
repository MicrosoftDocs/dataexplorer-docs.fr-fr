---
title: plugin pivot - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le plugin pivot dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: e4ec5a94483ade822280ee4a71106c214bb4b9a5
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511099"
---
# <a name="pivot-plugin"></a>plugin pivot

Tourne une table en transformant les valeurs uniques d’une colonne dans la table d’entrée en plusieurs colonnes dans le tableau de sortie, et effectue des agrégations là où elles sont nécessaires sur les valeurs de colonne restantes qui sont recherchées dans la sortie finale.

```kusto
T | evaluate pivot(PivotColumn)
```

**Syntaxe**

`T | evaluate pivot(`*pivotColumn*`[, `*agrégationFunction*`] [,`*colonne1* `[,` *colonne2* ...`]])`

**Arguments**

* *pivotColumn*: La colonne à tourner. chaque valeur unique de cette colonne sera une colonne dans le tableau de sortie.
* *fonction d’agrégation*: (facultatif) agrège plusieurs lignes dans le tableau d’entrée à une seule rangée dans le tableau de sortie. Fonctions actuellement `min()`prises `max()` `any()`en `sum()` `dcount()`charge: `stdev()` `variance()`, `count()` , , `count()`, `avg()`, , , et (par défaut est ).
* *colonne1*, *colonne2*, ...: noms de colonnes (facultatifs). Le tableau de sortie contiendra une colonne supplémentaire par chaque colonne spécifiée. par défaut : toutes les colonnes autres que la colonne pivotée et la colonne d’agrégation.

**Retourne**

Pivot renvoie la table pivotée avec des colonnes spécifiées *(colonne1*, *colonne2*, ...) ainsi que toutes les valeurs uniques des colonnes pivot. Chaque cellule pour les colonnes pivotées contiendra le calcul de la fonction globale.

**Remarque**

Le schéma de `pivot` sortie du plugin est basé sur les données et donc la requête peut produire des schémas différents pour deux pistes. Cela signifie également que la requête qui fait référence aux colonnes non emballées peut devenir «cassée» à tout moment. Pour cette raison - il n’est pas conseillé d’utiliser ce plugin pour les travaux d’automatisation.

**Exemples**

### <a name="pivot-by-a-column"></a>Pivot par une colonne

Pour chaque EventType et États à partir de 'AL', comptez le nombre d’événements de ce type dans cet état.

```kusto
StormEvents
| project State, EventType 
| where State startswith "AL" 
| where EventType has "Wind" 
| evaluate pivot(State)
```

|Type d’événement|ALABAMA|Alaska|
|---|---|---|
|Vent d’orage|352|1|
|Vent élevé|0|95|
|Froid extrême/refroidissement éolien|0|10|
|Vent fort|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>Pivotez par une colonne avec fonction d’agrégation.

Pour chaque EventType et États à partir de 'AR', affichez le nombre total de décès directs.

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|Type d’événement|Arkansas|Arizona|
|---|---|---|
|Pluie abondante|1|0|
|Vent d’orage|1|0|
|Foudre|0|1|
|Crue soudaine|0|6|
|Vent fort|1|0|
|Chauffage|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>Pivotez par une colonne avec fonction d’agrégation et une seule colonne supplémentaire.

Le résultat est identique à l’exemple précédent.

```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|Type d’événement|Arkansas|Arizona|
|---|---|---|
|Pluie abondante|1|0|
|Vent d’orage|1|0|
|Foudre|0|1|
|Crue soudaine|0|6|
|Vent fort|1|0|
|Chauffage|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>Spécifier la colonne pivotée, la fonction d’agrégation et plusieurs colonnes supplémentaires.

Pour chaque type d’événement, source et état, résumez le nombre de décès directs.

```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|Type d’événement|Source|Arkansas|Arizona|
|---|---|---|---|
|Pluie abondante|Gestionnaire des urgences|1|0|
|Vent d’orage|Gestionnaire des urgences|1|0|
|Foudre|Journal|0|1|
|Crue soudaine|Observateur chevronné|0|2|
|Crue soudaine|Médias audiovisuels|0|3|
|Crue soudaine|Journal|0|1|
|Vent fort|Respect des lois|1|0|
|Chauffage|Journal|3|0|