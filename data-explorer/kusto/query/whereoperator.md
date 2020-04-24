---
title: où l’opérateur (a, contient, commence, se termine, correspond regex) - Azure Data Explorer ( Microsoft Docs
description: Cet article décrit où l’opérateur (a, a, contient, commence, se termine, correspond regex) dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a0e1486691740600fb5de4316982e8d43b13ce3a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81504299"
---
# <a name="where-operator-has-contains-startswith-endswith-matches-regex"></a>où l’opérateur (a, contient, commence, se termine, correspond regex)

Filtre une table d’après le sous-ensemble de lignes correspondant à un prédicat.

```kusto
T | where fruit=="apple"
```

**Alias** `filter`

**Syntaxe**

*T* `| where` *Predicate*

**Arguments**

* *T*: L’entrée tabulaire dont les enregistrements doivent être filtrés.
* *Predicate*: `boolean` Une [expression](./scalar-data-types/bool.md) sur les colonnes de *T*. Il est évalué pour chaque rangée en *T*.

**Retourne**

Les lignes de *T* dont *Predicate* est `true`.

**Notes** Valeurs nulles : toutes les fonctions filtrantes reviennent fausses par rapport aux valeurs nulles. Vous pouvez utiliser des fonctions spéciales à ne pas savoir pour écrire des requêtes qui prennent en compte les valeurs nulles: [isnull()](./isnullfunction.md), [isnotnull()](./isnotnullfunction.md), [isempty()](./isemptyfunction.md), [isnotempty()](./isnotemptyfunction.md). 

**Conseils**

Pour obtenir des performances optimales :

* **Utilisez des comparaisons simples** entre les noms de colonne et les constantes. ('Constant' signifie constant sur `now()` la `ago()` table - donc et sont OK, et sont donc des valeurs scalaires assignées à l’aide d’une [ `let` déclaration](./letstatement.md).)

    Par exemple, préférez `where Timestamp >= ago(1d)` à `where floor(Timestamp, 1d) == ago(1d)`.

* **Simplest terms first** (termes les plus simples en premier) : si vous avez plusieurs clauses unies par `and`, insérez d’abord les clauses n’impliquant qu’une seule colonne. C’est pourquoi `Timestamp > ago(1d) and OpId == EventId` est plus adapté.

Pour plus d’informations, consultez le résumé des [opérateurs de cordes disponibles](./datatypes-string-operators.md) et le résumé des [opérateurs numériques disponibles.](./numoperators.md)

**Exemple**

```kusto
Traces
| where Timestamp > ago(1h)
    and Source == "MyCluster"
    and ActivityId == SubActivityId 
```

Les enregistrements qui ne sont pas plus d’une heure, et proviennent de la Source appelée "MyCluster", et ont deux colonnes de la même valeur. 

Notez que nous plaçons la comparaison entre deux colonnes à la fin, car elle ne peut pas utiliser l’index et force une analyse.

**Exemple**

```kusto
Traces | where * has "Kusto"
```

Toutes les lignes dans lesquelles le mot "Kusto" apparaît dans n’importe quelle colonne.