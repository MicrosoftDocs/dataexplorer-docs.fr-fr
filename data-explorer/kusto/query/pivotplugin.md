---
title: plug-in pivot-Azure Explorateur de données
description: Cet article décrit le plug-in pivot dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a046cc369dd466defa50916ee78b2c29f5f88ea0
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373217"
---
# <a name="pivot-plugin"></a>plug-in pivot

Fait pivoter une table en transformant les valeurs uniques d’une colonne de la table d’entrée en plusieurs colonnes dans la table de sortie, et effectue des agrégations lorsqu’elles sont requises sur les valeurs de colonne restantes qui sont souhaitées dans la sortie finale.

```kusto
T | evaluate pivot(PivotColumn)
```

**Syntaxe**

`T | evaluate pivot(`*pivotColumn* `[, ` *aggregationFunction* `] [,` *Colonne1* `[,` *Column2* ...`]])`

**Arguments**

* *pivotColumn*: colonne à faire pivoter. chaque valeur unique de cette colonne sera une colonne dans la table de sortie.
* *fonction d’agrégation*: (facultatif) agrège plusieurs lignes de la table d’entrée en une seule ligne dans la table de sortie. Fonctions actuellement prises en charge : `min()` , `max()` ,, `any()` `sum()` , `dcount()` , `avg()` , `stdev()` , `variance()` et `count()` (la valeur par défaut est `count()` ).
* noms de colonne *Colonne1*, *Column2*,... : (facultatif). La table de sortie contient une colonne supplémentaire pour chaque colonne spécifiée. valeur par défaut : toutes les colonnes autres que la colonne de tableau croisé dynamique et la colonne d’agrégation.

**Retourne**

Pivot renvoie la table pivotée avec les colonnes spécifiées (*Colonne1*, *Column2*,...), ainsi que toutes les valeurs uniques des colonnes de tableau croisé dynamique. Chaque cellule des colonnes croisées dynamiques contient le calcul de la fonction d’agrégation.

**Remarque**

Le schéma de sortie du `pivot` plug-in est basé sur les données et, par conséquent, la requête peut produire un schéma différent pour deux exécutions. Cela signifie également que la requête qui fait référence à des colonnes décompressées peut devenir « interrompue » à tout moment. Pour cette raison, il n’est pas recommandé d’utiliser ce plug-in pour les travaux d’automatisation.

**Exemples**

### <a name="pivot-by-a-column"></a>Tableau croisé dynamique par colonne

Pour chaque EventType et chaque État commençant par « AL », comptez le nombre d’événements de ce type dans cet État.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| project State, EventType 
| where State startswith "AL" 
| where EventType has "Wind" 
| evaluate pivot(State)
```

|Type d’événement|ALABAMA|ALASKA|
|---|---|---|
|Vent d’orage|352|1|
|Vent élevé|0|95|
|Froid extrême froid|0|10|
|Vent fort|22|0|


### <a name="pivot-by-a-column-with-aggregation-function"></a>Pivot par une colonne avec fonction d’agrégation.

Pour chaque EventType et chaque État commençant par « AR », affichez le nombre total de décès directs.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect))
```

|Type d’événement|ARKANSAS|ARIZONA|
|---|---|---|
|Pluie lourde|1|0|
|Vent d’orage|1|0|
|Orage|0|1|
|Crue soudaine|0|6|
|Vent fort|1|0|
|Chauffage|3|0|


### <a name="pivot-by-a-column-with-aggregation-function-and-a-single-additional-column"></a>Pivot par une colonne avec fonction d’agrégation et une colonne supplémentaire unique.

Le résultat est identique à l’exemple précédent.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| project State, EventType, DeathsDirect 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType)
```

|Type d’événement|ARKANSAS|ARIZONA|
|---|---|---|
|Pluie lourde|1|0|
|Vent d’orage|1|0|
|Orage|0|1|
|Crue soudaine|0|6|
|Vent fort|1|0|
|Chauffage|3|0|


### <a name="specify-the-pivoted-column-aggregation-function-and-multiple-additional-columns"></a>Spécifiez la colonne pivotée, la fonction d’agrégation et plusieurs colonnes supplémentaires.

Pour chaque type d’événement, source et état, additionner le nombre de décès directs.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State startswith "AR" 
| where DeathsDirect > 0
| evaluate pivot(State, sum(DeathsDirect), EventType, Source)
```

|Type d’événement|Source|ARKANSAS|ARIZONA|
|---|---|---|---|
|Pluie lourde|Gestionnaire des urgences|1|0|
|Vent d’orage|Gestionnaire des urgences|1|0|
|Orage|Journal|0|1|
|Crue soudaine|Observateur chevronné|0|2|
|Crue soudaine|Média de diffusion|0|3|
|Crue soudaine|Journal|0|1|
|Vent fort|Respect des lois|1|0|
|Chauffage|Journal|3|0|
