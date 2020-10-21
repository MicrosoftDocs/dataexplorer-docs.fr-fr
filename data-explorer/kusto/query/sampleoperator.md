---
title: exemple d’opérateur-Azure Explorateur de données
description: Cet article décrit l’exemple d’opérateur dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/18/2020
ms.openlocfilehash: 35495b9541f7dac35bb1aa45cbc435e7f3fedfad
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242720"
---
# <a name="sample-operator"></a>opérateur d’échantillon

Retourne jusqu’au nombre spécifié de lignes aléatoires de la table d’entrée.

```kusto
T | sample 5
```

> [!NOTE]
> * `sample` est adapté à la vitesse plutôt qu’à la distribution des valeurs. Plus précisément, cela signifie qu’il ne produira pas de résultats « justes » s’ils sont utilisés après les opérateurs qui Union 2 ont des jeux de données de différentes tailles (tels que les `union` `join` opérateurs ou). Il est recommandé d’utiliser `sample` juste après la référence de table et les filtres.
> * `sample` est un opérateur non déterministe qui retourne un jeu de résultats différent chaque fois qu’il est évalué au cours de la requête. Par exemple, la requête suivante génère deux lignes différentes (même si on s’attend à renvoyer deux fois la même ligne).

## <a name="syntax"></a>Syntaxe

*T* `| sample` *numberOfRows*

## <a name="arguments"></a>Arguments

* *NumberOfRows*: nombre de lignes de *T* à retourner. Vous pouvez spécifier n’importe quelle expression numérique.

## <a name="examples"></a>Exemples

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = _data | sample 1;
union (_sample), (_sample)
```

| x   |
| --- |
| 83  |
| 3   |

Pour vous assurer que dans l’exemple ci-dessus `_sample` est calculé une seule fois, vous pouvez utiliser la fonction [matérialiser ()](./materializefunction.md) :

```kusto
let _data = range x from 1 to 100 step 1;
let _sample = materialize(_data | sample 1);
union (_sample), (_sample)
```

| x   |
| --- |
| 34  |
| 34  |

Pour échantillonner un certain pourcentage de vos données (au lieu d’un nombre de lignes spécifié), vous pouvez utiliser

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | where rand() < 0.1
```

Pour échantillonner des clés plutôt que des lignes (par exemple, exemples de 10 ID et obtenir toutes les lignes de ces ID), vous pouvez utiliser [`sample-distinct`](./sampledistinctoperator.md) en association avec l' `in` opérateur.


<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents
| where EpisodeId in (sampleEpisodes)
```
