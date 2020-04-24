---
title: row_number() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit row_number() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c8cb01ed098d24632154215ddf06dc2ab1d72695
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510164"
---
# <a name="row_number"></a>row_number()

Retourne l’indice de la ligne actuelle dans un [ensemble de lignes sérialisées](./windowsfunctions.md#serialized-row-set).
L’indice de ligne `1` commence par défaut à la première `1` rangée, et est incrémenté par pour chaque rangée supplémentaire.
Optionnellement, l’indice de ligne `1`peut commencer à une valeur différente de .
En outre, l’indice de ligne peut être réinitialisé en fonction de certains prédicat fournis.

**Syntaxe**

`row_number``(` [*StartingIndex* [`,` *Redémarrer*]]`)`

* *StartingIndex* est une expression `long` constante de type indiquant la valeur de l’indice de ligne pour commencer (ou pour redémarrer à). La valeur par défaut est `1`.
* *Le redémarrage* est un `bool` argument facultatif de type qui indique quand la numérotation doit être redémarrée à la valeur *StartingIndex.* Si elle n’est `false` pas fournie, la valeur par défaut est utilisée.

**Retourne**

La fonction renvoie l’indice de ligne `long`de la ligne actuelle comme une valeur de type .

**Exemples**

L’exemple suivant renvoie un tableau avec`a`deux colonnes, `1`la première colonne`rn`( ) `1` avec des nombres de `10` vers le bas à , et la deuxième colonne ( ) avec des nombres allant jusqu’à `10`:

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number()
```

L’exemple suivant est similaire à ce`rn`qui précède, seule la deuxième colonne ( ) commence à `7`:

```kusto
range a from 1 to 10 step 1
| sort by a desc
| extend rn=row_number(7)
```

Le dernier exemple montre comment on peut diviser les données et numéroer les lignes par chaque partition. Ici, nous partitions `Airport`les données par :

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

Exécution de cette requête produit le résultat suivant:

Aéroport  | Compagnie aérienne  | Départs  | Rank
---------|----------|-------------|------
SEA      | BA       | 2           | 1
SEA      | Lh       | 1           | 2
SEA      | LY       | 0           | 3
Tlv      | LY       | 100         | 1
Tlv      | Lh       | 1           | 2