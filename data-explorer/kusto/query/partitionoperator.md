---
title: opérateur de partition - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de partition dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0a9ef31f68a989fffe42d9b54800e9305e51f4ed
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511371"
---
# <a name="partition-operator"></a>partition, opérateur

L’opérateur de partition divise sa table d’entrée en plusieurs sous-tables selon les valeurs de la colonne spécifiée, exécute une sous-requête sur chaque sous-table et produit une table de sortie unique qui est l’union des résultats de toutes les sous-requêtes. 

```kusto
T | partition by Col1 ( top 10 by MaxValue )

T | partition by Col1 { U | where Col2=toscalar(Col1) }
```

**Syntaxe**

*T* `|` T `partition` [*PartitionParameters*] `by` *Colonne* `(` *ContextualSubquery*`)`

*T* `|` T `partition` [*PartitionParameters*] `by` *Sous-laquery* *Colonne* `{``}`

**Arguments**

* *T*: La source tabulaire dont les données doivent être traitées par l’opérateur.

* *Colonne*: Nom d’une colonne dans *T* dont les valeurs déterminent comment la table d’entrée doit être divisée. Voir **notes** ci-dessous.

* *ContextualSubquery*: Une expression tabulaire, dont `partition` la source est la source de l’opérateur, portée pour une seule valeur *clé.*

* *Sous-laquery*: Une expression tabulaire sans source. La valeur *clé* peut `toscalar()` être obtenue par appel.

* *PartitionParameters*: Paramètres nuls ou plus (séparés dans l’espace) sous la forme de : *Valeur nominative* `=` *Value* qui contrôle le comportement de l’opérateur. Les paramètres suivants sont pris en charge :

  |Nom               |Valeurs         |Description|
  |-------------------|---------------|-----------|
  |`hint.materialized`|`true`,`false` |Si prévu `true` pour matérialiser `partition` la source `false`de l’opérateur (par défaut: )|
  |`hint.concurrency`|*Nombre*|Indique au système le nombre de `partition` sous-cas simultanés de l’opérateur doivent être exécutés en parallèle. *Défaut*: Quantité de cœurs CPU sur le nœud unique du cluster (2 à 16).|
  |`hint.spread`|*Nombre*|Indique au système combien de nœuds`partition` doivent être utilisés par l’exécution simultanée des sous-ques. *Défaut*: 1.|

**Retourne**

L’opérateur retourne une union des résultats de l’application de la sous-query à chaque partition des données d’entrée.

**Remarques**

* L’opérateur de partition est actuellement limité par le nombre de partitions.
  Jusqu’à 64 partitions distinctes peuvent être créées.
  L’opérateur produira une erreur si la colonne de partition *(Colonne*) a plus de 64 valeurs distinctes.

* La sous-conception fait référence implicitement à la partition d’entrée (il n’y a pas de « nom » pour la partition dans la sous-pays). Pour référencer la partition d’entrée plusieurs fois dans la sous-pays, utilisez le [comme opérateur,](asoperator.md)comme dans **l’exemple: partition-référence ci-dessous.**

**Exemple : cas imbriqué au sommet**

Dans certains cas - il est plus performant `partition` et plus facile d’écrire des requêtes `top` à l’aide de `W`l’opérateur plutôt en utilisant [ `top-nested` l’opérateur](topnestedoperator.md) L’exemple suivant exécute un calcul de sous-requête `summarize` et pour chacun des États à commencer par : (WYOMING, WASHINGTON, WEST VIRGINIA, WISCONSIN)

```kusto
StormEvents
| where State startswith 'W'
| partition by State 
(
    summarize Events=count(), Injuries=sum(InjuriesDirect) by EventType, State
    | top 3 by Events 
) 

```
|Type d’événement|State|Événements|Blessures|
|---|---|---|---|
|Grêle|Wyoming|108|0|
|Vent élevé|Wyoming|81|5|
|Tempête hivernale|Wyoming|72|0|
|Neige lourde|Washington|82|0|
|Vent élevé|Washington|58|13|
|Wildfire|Washington|29|0|
|Vent d’orage|WEST VIRGINIA|180|1|
|Grêle|WEST VIRGINIA|103|0|
|Météo d’hiver|WEST VIRGINIA|88|0|
|Vent d’orage|Wisconsin|416|1|
|Tempête hivernale|Wisconsin|310|0|
|Grêle|Wisconsin|303|1|

**Exemple : requêtes non-overlapant des partitions de données**

Parfois, il est utile (perf-sage) d’exécuter une sous-querie complexe sur les partitions de données non-overlapping dans une carte / réduire le style. L’exemple ci-dessous montre comment créer une distribution manuelle de l’agrégation sur 10 partitions.

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

|Source|Count|
|---|---|
|Observateur chevronné|12770|
|Respect des lois|8570|
|Public|6157|
|Gestionnaire des urgences|4900|
|Observateur COOP|3039|

**Exemple : partitionnement de temps de requête**

L’exemple suivant montre comment la requête peut être divisée en partitions N-10, où chaque partition calcule son propre comte, et le tout résumé plus tard dans TotalCount.

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


**Exemple : partition-référence**

L’exemple suivant montre comment on peut utiliser [l’opérateur en tant qu’opérateur](asoperator.md) pour donner un « nom » à chaque partition de données, puis réutiliser ce nom dans la sous-fertilité :

```kusto
T
| partition by Dim
(
    as Partition
    | extend MetricPct = Metric * 100.0 / toscalar(Partition | summarize sum(Metric))
)
```

**Exemple : sous-fertilité complexe cachée par un appel de fonction**

La même technique peut être appliquée avec des sous-queries beaucoup plus complexes. Pour simplifier la syntaxe, on peut envelopper la sous-query dans un appel de fonction :

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

|Source|Count|
|---|---|
|Observateur chevronné|12770|
|Respect des lois|8570|
|Public|6157|
|Gestionnaire des urgences|4900|
|Observateur COOP|3039|