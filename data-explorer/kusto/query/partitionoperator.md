---
title: opérateur de partition-Azure Explorateur de données
description: Cet article décrit l’opérateur de partition dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8587995a6836a1f8a180eada19d450277709a6e7
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92248597"
---
# <a name="partition-operator"></a>partition, opérateur

L’opérateur partition partitionne sa table d’entrée en plusieurs sous-tables en fonction des valeurs de la colonne spécifiée, exécute une sous-requête sur chaque sous-table et génère une seule table de sortie qui est l’Union des résultats de toutes les sous-requêtes. 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

## <a name="syntax"></a>Syntaxe

*T* `|` `partition` [*PartitionParameters*] `by` *colonne* `(` *ContextualSubquery*`)`

*T* `|` `partition` [*PartitionParameters*] `by` *Column* `{` *sous-requête* de colonne`}`

## <a name="arguments"></a>Arguments

* *T*: source tabulaire dont les données doivent être traitées par l’opérateur.

* *Colonne*: le nom d’une colonne dans *T* dont les valeurs déterminent la façon dont la table d’entrée doit être partitionnée. Consultez les **Remarques** ci-dessous.

* *ContextualSubquery*: expression tabulaire, qui est la source de l' `partition` opérateur, définie pour une valeur de *clé* unique.

* *Sous-requête*: expression tabulaire sans source. La valeur de *clé* peut être obtenue via un `toscalar()` appel.

* *PartitionParameters*: zéro ou plusieurs paramètres (séparés par des espaces) sous la forme : *nom* `=` *valeur* qui contrôle le comportement de l’opérateur. Les paramètres suivants sont pris en charge :

  |Nom               |Valeurs         |Description|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |Si la valeur `true` est, la source de l’opérateur est matérialisée `partition` (valeur par défaut : `false` )|
  |`hint.concurrency`|*Nombre*|Indique au système le nombre de sous-requêtes simultanées de l' `partition` opérateur qui doivent être exécutées en parallèle. *Valeur par défaut*: quantité de cœurs de processeur sur le nœud unique du cluster (2 à 16).|
  |`hint.spread`|*Nombre*|Indique au système le nombre de nœuds qui doivent être utilisés par l’exécution simultanée des sous- `partition` requêtes. *Valeur par défaut*: 1.|

## <a name="returns"></a>Retours

L’opérateur retourne une Union des résultats de l’application de la sous-requête à chaque partition des données d’entrée.

**Notes**

* L’opérateur de partition est actuellement limité par le nombre de partitions.
  Jusqu’à 64 partitions distinctes peuvent être créées.
  L’opérateur génère une erreur si la colonne de partition (*colonne*) a plus de 64 valeurs distinctes.

* La sous-requête fait référence à la partition d’entrée de manière implicite (il n’y a pas de « nom » pour la partition dans la sous-requête). Pour référencer la partition d’entrée plusieurs fois dans la sous-requête, utilisez l' [opérateur as](asoperator.md), comme dans l' **exemple : partition-Reference** ci-dessous.

**Exemple : cas imbriqué en haut**

Dans certains cas, il est plus performant et plus facile d’écrire des requêtes à l’aide de l’opérateur `partition` plutôt que d’utiliser [ `top-nested` Operator](topnestedoperator.md) . l’exemple suivant exécute une sous-requête qui calcule `summarize` et `top` for-each des États à partir de `W` : (Wyoming, Washington, West Virginie, Wisconsin)

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|Type d’événement|State|Événements|Blesser|
|---|---|---|---|
|Grêle|WYOMING|108|0|
|Vent élevé|WYOMING|81|5|
|Tempête d’hiver|WYOMING|72|0|
|Gros neige|WASHINGTON|82|0|
|Vent élevé|WASHINGTON|58|13|
|Feux|WASHINGTON|29|0|
|Vent d’orage|WEST VIRGINIA|180|1|
|Grêle|WEST VIRGINIA|103|0|
|Hiver hiver|WEST VIRGINIA|88|0|
|Vent d’orage|WISCONSIN|416|1|
|Tempête d’hiver|WISCONSIN|310|0|
|Grêle|WISCONSIN|303|1|

**Exemple : interroger des partitions de données qui ne se chevauchent pas**

Il est parfois utile (au niveau des performances) d’exécuter une sous-requête complexe sur des partitions de données qui ne se chevauchent pas dans un style de carte/réduction. L’exemple ci-dessous montre comment créer une répartition manuelle de l’agrégation sur 10 partitions.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    summarize Count=count() by Source 
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|Source|Nombre|
|---|---|
|Observateur chevronné|12770|
|Respect des lois|8570|
|Publiques|6157|
|Gestionnaire des urgences|4900|
|Observateur COOP|3039|

**Exemple : partitionnement au moment de la requête**

L’exemple suivant illustre la façon dont la requête peut être partitionnée en N = 10 partitions, où chaque partition calcule son propre nombre et est ensuite résumée dans TotalCount.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let N = 10;                 // Number of query-partitions
range p from 0 to N-1 step 1  // 
| partition by p            // Run the sub-query partitioned 
{
    StormEvents 
    | where hash(EventId, N) == toscalar(p) // Use toscalar() to fetch partition key value
    | summarize Count = count()
}
| summarize TotalCount=sum(Count) 
```

|TotalCount|
|---|
|59066|


**Exemple : partition-Reference**

L’exemple suivant montre comment utiliser l' [opérateur as](asoperator.md) pour attribuer un « nom » à chaque partition de données, puis réutiliser ce nom dans la sous-requête :

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**Exemple : sous-requête complexe masquée par un appel de fonction**

La même technique peut être appliquée avec des sous-requêtes bien plus complexes. Pour simplifier la syntaxe, il est possible d’encapsuler la sous-requête dans un appel de fonction :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let partition_function = (T:(Source:string)) 
{
    T
    | summarize Count=count() by Source
};
StormEvents
| extend p = hash(EventId, 10)
| partition by p
(
    invoke partition_function()
)
| summarize Count=sum(Count) by Source
| top 5 by Count
```

|Source|Nombre|
|---|---|
|Observateur chevronné|12770|
|Respect des lois|8570|
|Publiques|6157|
|Gestionnaire des urgences|4900|
|Observateur COOP|3039|