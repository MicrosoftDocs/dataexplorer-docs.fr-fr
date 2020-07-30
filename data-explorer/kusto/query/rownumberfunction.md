---
title: row_number ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit row_number () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea51e6171b8a7683a0454d177dc729ed754b8896
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351588"
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

## <a name="returns"></a>Retourne

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