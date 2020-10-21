---
title: row_number ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit row_number () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 457e9445aa113e76052b9c4d96019352215d08f9
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242805"
---
# <a name="row_number"></a>row_number()

Retourne l’index de la ligne actuelle dans un [jeu de lignes sérialisé](./windowsfunctions.md#serialized-row-set).
L’index de ligne commence par défaut à `1` pour la première ligne et est incrémenté par `1` pour chaque ligne supplémentaire.
Si vous le souhaitez, l’index de ligne peut commencer à une valeur différente de celle de `1` .
En outre, l’index de ligne peut être réinitialisé selon un prédicat fourni.

## <a name="syntax"></a>Syntaxe

`row_number``(`[*StartingIndex* [ `,` *restart*]]`)`

* *StartingIndex* est une expression constante de type `long` indiquant la valeur de l’index de ligne à partir duquel commencer (ou à redémarrer). La valeur par défaut est `1`.
* *Restart* est un argument facultatif de type `bool` qui indique quand la numérotation doit être redémarrée à la valeur *StartingIndex* . S’il n’est pas fourni, la valeur par défaut de `false` est utilisée.

## <a name="returns"></a>Retours

La fonction retourne l’index de ligne de la ligne actuelle en tant que valeur de type `long` .

## <a name="examples"></a>Exemples

L’exemple suivant retourne une table avec deux colonnes, la première colonne ( `a` ) avec des nombres compris entre `10` `1` et la deuxième colonne ( `rn` ) avec des nombres allant `1` jusqu’à `10` :

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

L’exemple suivant est similaire à l’exemple ci-dessus, seule la deuxième colonne ( `rn` ) commence à `7` :

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

Le dernier exemple montre comment partitionner les données et numéroter les lignes pour chaque partition. Ici, nous allons partitionner les données de la façon `Airport` suivante :

```kusto
datatable (Airport:string, Airline:string, Departures:long)
[
  "TLV", "LH", 1,
  "TLV", "LY", 100,
  "SEA", "LH", 1,
  "SEA", "BA", 2,
  "SEA", "LY", 0
]
| sort by Airport asc, Departures desc
| extend Rank=row_number(1, prev(Airport) != Airport)
```

L’exécution de cette requête produit le résultat suivant :

Aeroport  | Compagnie aérienne  | Départs  | Rank
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | LH       | 1           | 2
SEA      | LY       | 0           | 3
REÇU      | LY       | 100         | 1
REÇU      | LH       | 1           | 2