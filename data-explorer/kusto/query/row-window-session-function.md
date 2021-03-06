---
title: row_window_session ()-Azure Explorateur de données
description: Cet article décrit row_window_session () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f872004a8291adc95f594c6301075faa02c8ec6c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242868"
---
# <a name="row_window_session"></a>row_window_session()

`row_window_session()` calcule les valeurs de début de session d’une colonne dans un [ensemble de lignes sérialisé](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Syntaxe

`row_window_session` `(` *`Expr`* `,` *`MaxDistanceFromFirst`* `,` *`MaxDistanceBetweenNeighbors`* [`,` *`Restart`*] `)`

* *`Expr`* expression dont les valeurs sont regroupées dans des sessions.
  Les valeurs NULL produisent des valeurs NULL, et la valeur suivante démarre une nouvelle session.
  *`Expr`* doit être une expression scalaire de type `datetime` .

* *`MaxDistanceFromFirst`* établit un critère pour démarrer une nouvelle session : la distance maximale entre la valeur actuelle de *`Expr`* et la valeur de *`Expr`* au début de la session.
  Il s’agit d’une constante scalaire de type `timespan` .

* *`MaxDistanceBetweenNeighbors`* établit un deuxième critère pour démarrer une nouvelle session : la distance maximale d’une valeur de *`Expr`* à la suivante.
  Il s’agit d’une constante scalaire de type `timespan` .

* *Restart* est une expression scalaire facultative de type `boolean` . Si cette valeur est spécifiée, chaque valeur qui correspond à `true` redémarrera immédiatement la session.

## <a name="returns"></a>Retours

La fonction retourne les valeurs au début de chaque session.

**Notes**

La fonction a le modèle de calcul conceptuel suivant :

1. Passez en revue la séquence d’entrée des valeurs *`Expr`* dans l’ordre.

1. Pour chaque valeur, déterminez si elle établit une nouvelle session.

1. S’il établit une nouvelle session, émettez la valeur de *`Expr`* . Sinon, émettez la valeur précédente de *`Expr`* .

La condition qui détermine si la valeur représente une nouvelle session est une ou logique de l’une des conditions suivantes :

* S’il n’y avait pas de valeur de session précédente ou si la valeur de session précédente était null.

* Si la valeur de *`Expr`* est égale ou supérieure à la valeur de session précédente plus *`MaxDistanceFromFirst`* .

* Si la valeur de *`Expr`* est égale ou supérieure à la valeur précédente de *`Expr`* plus *`MaxDistanceBetweenNeighbors`* .

## <a name="examples"></a>Exemples

L’exemple suivant montre comment calculer les valeurs de début de session pour une table avec deux colonnes : une `ID` colonne qui identifie une séquence et une `Timestamp` colonne qui indique l’heure à laquelle chaque enregistrement s’est produit. Dans cet exemple, une session ne peut pas dépasser 1 heure et se poursuit tant que les enregistrements sont séparés de moins de 5 minutes.

```kusto
datatable (ID:string, Timestamp:datetime) [
    // ...
]
| sort by ID asc, Timestamp asc
| extend SessionStarted = row_window_session(Timestamp, 1h, 5m, ID != prev(ID))
```