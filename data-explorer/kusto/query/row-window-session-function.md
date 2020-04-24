---
title: row_window_session() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit row_window_session() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 87e89443bc85125e705bc180ea0cdb9e599c9c13
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510249"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()`calcule les valeurs de début de session d’une colonne dans un [ensemble de lignes sérialisés](./windowsfunctions.md#serialized-row-set).

**Syntaxe**

`row_window_session``(` *Expr* `,` *MaxDistanceFromFirst* `,` *MaxDistanceBetweenNeighbors* [`,` *Redémarrer*]`)`

* *Expr* est une expression dont les valeurs sont regroupées en sessions.
  Les valeurs nulles produisent des valeurs nulles, et la valeur suivante commence une nouvelle session.
  *Expr* doit être une expression `datetime`scalaire de type .

* *MaxDistanceFromFirst* établit un critère pour commencer une nouvelle session : la distance maximale entre la valeur actuelle *d’Expr* et la valeur *d’Expr* au début de la session.
  Il s’agit d’une `timespan`constante scalaire de type .

* *MaxDistanceBetweenNeighbors* établit un deuxième critère pour commencer une nouvelle session : la distance maximale d’une valeur *d’Expr* à l’autre.
  Il s’agit d’une `timespan`constante scalaire de type .

* *Le redémarrage* est une expression `boolean`scalaire facultative de type . Si spécifié, chaque valeur `true` qui s’évalue pour redémarrer immédiatement la session.

**Retourne**

La fonction renvoie les valeurs au début de chaque session.

**Remarques**

La fonction a le modèle de calcul conceptuel suivant :

1. Passez en compte la séquence d’entrée des valeurs *Expr* dans l’ordre.

2. Pour chaque valeur, déterminez si elle établit une nouvelle session.

3. S’il établit une nouvelle session, émettez la valeur *d’Expr*. Sinon, émettez la valeur précédente *d’Expr*.

La condition que la valeur détermine si la valeur représente une nouvelle session est une OR logique de ce qui suit:

* S’il n’y avait pas de valeur de session précédente ou si la valeur de la session précédente était nulle.

* Si la valeur *d’Expr* est égale ou dépasse la valeur de session précédente plus *MaxDistanceFromFirst*.

* Si la valeur *d’Expr* est égale ou dépasse la valeur précédente *d’Expr* plus *MaxDistanceBetweenNeighbors*.

**Exemples**

L’exemple suivant montre comment calculer les valeurs de début `ID` de session pour une `Timestamp` table avec deux colonnes, une colonne qui identifie une séquence, et une colonne qui donne l’heure à laquelle chaque enregistrement s’est produit. Dans cet exemple, une session ne peut pas dépasser 1 heure, et elle se poursuit tant que les enregistrements sont à moins de 5 minutes d’intervalle.

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```