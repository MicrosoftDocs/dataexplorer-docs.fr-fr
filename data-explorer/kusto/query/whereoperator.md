---
title: opérateur Where dans le langage de requête Kusto-Azure Explorateur de données
description: Cet article décrit l’opérateur Where dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 116f585fe8a09b4befddd7d1429229ad1bac886f
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512468"
---
# <a name="where-operator"></a>opérateur where

Filtre une table d’après le sous-ensemble de lignes correspondant à un prédicat.

```kusto
T | where fruit=="apple"
```

**Alias** `filter`

**Syntaxe**

*T* `| where` *Prédicat* T

**Arguments**

* *T*: entrée tabulaire dont les enregistrements doivent être filtrés.
* *Predicate*: `boolean` [expression](./scalar-data-types/bool.md) sur les colonnes de *T*. Elle est évaluée pour chaque ligne dans *T*.

**Retourne**

Les lignes de *T* dont *Predicate* est `true`.

**Notes** Valeurs NULL : toutes les fonctions de filtrage retournent false en cas de comparaison avec les valeurs NULL. Vous pouvez utiliser des fonctions spéciales prenant en charge les valeurs NULL pour écrire des requêtes qui gèrent des valeurs NULL.

[IsNull ()](./isnullfunction.md), [IsNotNull ()](./isnotnullfunction.md), [IsEmpty ()](./isemptyfunction.md), [IsNotEmpty ()](./isnotemptyfunction.md). 

**Conseils**

Pour obtenir des performances optimales :

* **Utilisez des comparaisons simples** entre les noms de colonne et les constantes. ('Constant’signifie constante sur la table `now()` , et sont donc des `ago()` valeurs scalaires affectées à l’aide d’une [ `let` instruction](./letstatement.md).)

    Par exemple, préférez `where Timestamp >= ago(1d)` à `where floor(Timestamp, 1d) == ago(1d)`.

* **Simplest terms first** (termes les plus simples en premier) : si vous avez plusieurs clauses unies par `and`, insérez d’abord les clauses n’impliquant qu’une seule colonne. C’est pourquoi `Timestamp > ago(1d) and OpId == EventId` est plus adapté.

Pour plus d’informations, consultez le résumé des [opérateurs de chaîne disponibles](./datatypes-string-operators.md) et le résumé des [opérateurs numériques disponibles](./numoperators.md).

**Exemple**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

Cet exemple récupère les enregistrements qui ne sont pas antérieurs à 1 heure, proviennent d’une source appelée `MyCluster` et ont deux colonnes de la même valeur. 

Notez que nous avons placé la comparaison entre deux colonnes en dernier, car elle ne peut pas utiliser l’index et force une analyse.

**Exemple**

```kusto
Traces | where * has "Kusto"
```

Toutes les lignes dans lesquelles le mot « Kusto » apparaît dans une colonne.
 
