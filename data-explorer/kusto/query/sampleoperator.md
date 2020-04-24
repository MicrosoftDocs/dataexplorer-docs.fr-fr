---
title: opérateur d’échantillons - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur d’échantillons dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 757830bde0c56ac727d5240c01ca4768eab28877
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510028"
---
# <a name="sample-operator"></a>opérateur d’échantillon

Retourne jusqu’au nombre spécifié de lignes aléatoires à partir de la table d’entrée.

```kusto
T | sample 5
```

**Syntaxe**

_T_ `| sample` _NumberOfRows_

**Arguments**

- _NumberOfRows_: Le nombre de rangées de _T_ à revenir. Vous pouvez spécifier n’importe quelle expression numérique.

**Remarques**

- `sample`est orientée vers la vitesse plutôt que même la distribution des valeurs. Plus précisément, cela signifie qu’il ne produira pas de résultats « équitables » s’il est utilisé après les opérateurs qui syndiques 2 ensembles de données de différentes tailles (comme un `union` ou `join` des opérateurs). Il est recommandé `sample` d’utiliser juste après la référence de table et les filtres.

- `sample`est un opérateur non déterministe, et retournera différents résultats définis chaque fois qu’il est évalué au cours de la requête. Par exemple, la requête suivante donne deux lignes différentes (même si l’on s’attendrait à retourner la même ligne deux fois).

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

Afin de s’assurer `_sample` que dans l’exemple ci-dessus est calculé une fois, on peut utiliser la fonction [matérialisée :)](./materializefunction.md) fonction:

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

**Conseils**

- si vous souhaitez échantillonner un certain pourcentage de vos données (plutôt qu’un nombre spécifié de lignes), vous pouvez

```kusto
StormEvents | where rand() < 0.1
```

- Si vous souhaitez échantillonner les touches plutôt que les lignes (par exemple - échantillonner 10 Ids et obtenir toutes les lignes pour ces Ids), vous pouvez utiliser [`sample-distinct`](./sampledistinctoperator.md) en combinaison avec l’opérateur. `in`

**Exemples**

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```