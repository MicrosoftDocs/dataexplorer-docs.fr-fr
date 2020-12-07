---
title: Opérateur where dans le langage de requête Kusto – Azure Data Explorer
description: Cet article décrit l’opérateur where dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 6ac800cd4b38396e0f32f44976c4594c093747bb
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512007"
---
# <a name="where-operator"></a>opérateur where

Filtre une table d’après le sous-ensemble de lignes correspondant à un prédicat.

```kusto
T | where fruit=="apple"
```

**Alias** `filter`

## <a name="syntax"></a>Syntaxe

*T* `| where` *Prédicat*

## <a name="arguments"></a>Arguments

* *T* : entrée tabulaire dont les enregistrements doivent être filtrés.
* *Prédicat* : [Expression](./scalar-data-types/bool.md) `boolean` sur les colonnes de *T*. Elle est évaluée pour chaque ligne dans *T*.

## <a name="returns"></a>Retours

Les lignes de *T* dont *Predicate* est `true`.

**Notes** Valeurs null : toutes les fonctions de filtrage retournent false en cas de comparaison avec des valeurs null. Vous pouvez utiliser des fonctions spéciales prenant en compte les valeurs null pour écrire des requêtes qui gèrent des valeurs null.

[isnull()](./isnullfunction.md), [isnotnull()](./isnotnullfunction.md), [isempty()](./isemptyfunction.md), [isnotempty()](./isnotemptyfunction.md). 

**Conseils**

Pour obtenir des performances optimales :

* **Utilisez des comparaisons simples** entre les noms de colonne et les constantes. (« Constante » s’entend dans le sens de constante au fil de la table, de telle sorte que `now()` et `ago()` soient OK, tout comme les valeurs scalaires affectées à l’aide d’une [instruction `let`](./letstatement.md).)

    Par exemple, préférez `where Timestamp >= ago(1d)` à `where floor(Timestamp, 1d) == ago(1d)`.

* **Simplest terms first** (termes les plus simples en premier) : si vous avez plusieurs clauses unies par `and`, insérez d’abord les clauses n’impliquant qu’une seule colonne. C’est pourquoi `Timestamp > ago(1d) and OpId == EventId` est plus adapté.

Pour plus d’informations, consultez le résumé des [opérateurs de chaîne disponibles](./datatypes-string-operators.md) et le résumé des [opérateurs numériques disponibles](./numoperators.md).

## <a name="example-simple-comparisons-first"></a>Exemple : Comparaisons simples en premier

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

Cet exemple récupère les enregistrements remontant à moins d’une heure, provenant d’une source nommée `MyCluster` et ayant deux colonnes de la même valeur. 

Notez que nous plaçons la comparaison entre deux colonnes à la fin, car elle ne peut pas utiliser l’index et force une analyse.

## <a name="example-columns-contain-string"></a>Exemple : Les colonnes contiennent une chaîne

```kusto
Traces | where * has "Kusto"
```

Toutes les lignes dans lesquelles le mot « Kusto » apparaît dans une colonne.
 
