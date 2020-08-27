---
title: Stratégie de partitionnement des données-Azure Explorateur de données
description: Cet article décrit la stratégie de partitionnement des données dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: cbafde1b87807c449923b8b010c57e3394c4a74f
ms.sourcegitcommit: d08b3344d7e9a6201cf01afc8455c7aea90335aa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88964742"
---
# <a name="data-partitioning-policy"></a>Stratégie de partitionnement des données

La stratégie de partitionnement définit si et comment les [étendues (données partitions)](../management/extents-overview.md) doivent être partitionnées pour une table spécifique.

L’objectif principal de la stratégie est d’améliorer les performances des requêtes qui sont connues pour limiter le jeu de données des valeurs dans les colonnes partitionnées, ou agréger/joindre sur une colonne de chaîne de cardinalité élevée. La stratégie peut également entraîner une meilleure compression des données.

> [!CAUTION]
> Aucune limite codée en dur n’est définie sur le nombre de tables sur lesquelles la stratégie peut être définie. Toutefois, chaque table supplémentaire ajoute une surcharge au processus de partitionnement des données en arrière-plan qui s’exécute sur les nœuds du cluster. Cela peut entraîner l’utilisation de plus de ressources de cluster. Pour plus d’informations, consultez [surveillance](#monitoring) et [capacité](#capacity).

## <a name="partition-keys"></a>Clés de partition

Les types de clés de partition suivants sont pris en charge.

|Type                                                   |Type de colonne |Propriétés de la partition                    |Valeur de partition                                        |
|-------------------------------------------------------|------------|----------------------------------------|----------------------|
|[Hachage](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[Plage uniforme](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>Clé de partition de hachage

> [!NOTE]
> Appliquez une clé de partition de hachage à une `string` colonne de type dans une table uniquement dans les cas suivants :
> * Si la majorité des requêtes utilisent des filtres d’égalité ( `==` , `in()` ).
> * La plupart des requêtes sont agrégées/jointes sur une `string` colonne de *grande dimension* (cardinalité de 10 millions ou supérieure), telle qu’un `application_ID` , un `tenant_ID` ou un `user_ID` .

* Une fonction de hachage-modulo est utilisée pour partitionner les données.
* Toutes les étendues homogènes (partitionnées) appartenant à la même partition sont affectées au même nœud de données.
* Les données dans des étendues homogènes (partitionnées) sont classées par la clé de partition de hachage.
  * Vous n’avez pas besoin d’inclure la clé de partition de hachage dans la [stratégie d’ordre des lignes](roworderpolicy.md), si celle-ci est définie sur la table.
* Les requêtes qui utilisent la [stratégie de lecture aléatoire](../query/shufflequery.md), et dans laquelle le `shuffle key` utilisé dans `join` , `summarize` ou `make-series` est la clé de partition de hachage de la table, sont censées fonctionner mieux, car la quantité de données requises pour le déplacement entre les nœuds de cluster est considérablement réduite.

#### <a name="partition-properties"></a>Propriétés de la partition

* `Function` nom d’une fonction de hachage-modulo à utiliser.
  * Valeur prise en charge : `XxHash64` .
* `MaxPartitionCount` nombre maximal de partitions à créer (l’argument modulo de la fonction de hachage-modulo) par période.
  * Les valeurs prises en charge sont comprises dans la plage `(1,1024]` .
    * La valeur doit être :
      * Plus de 5 fois le nombre de nœuds dans le cluster.
      * Plus petite que la cardinalité de la colonne.
    * Plus la valeur est élevée, plus la surcharge du processus de partitionnement des données sur les nœuds du cluster est importante et plus le nombre d’étendues pour chaque période de temps est élevé.
    * Pour les clusters contenant moins de 50 nœuds, nous vous recommandons de commencer par la valeur `256` .
      * Ajustez la valeur si nécessaire, en fonction des considérations ci-dessus (par exemple, le nombre de nœuds dans le cluster augmente), ou en fonction de l’avantage des performances des requêtes et de la surcharge liée au partitionnement de la publication des données.
* `Seed` valeur à utiliser pour la randomisation de la valeur de hachage.
  * La valeur doit être un entier positif.
  * La valeur recommandée est `1` , qui est la valeur par défaut, si elle n’est pas spécifiée.

#### <a name="example"></a>Exemple

Une clé de partition de hachage sur une `string` colonne de type typée nommée `tenant_id` .
Elle utilise la `XxHash64` fonction de hachage, avec un `MaxPartitionCount` de `256` et la valeur par défaut `Seed` de `1` .

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>Clé de partition DateTime de plage uniforme

> [!NOTE] 
> Appliquez uniquement une clé de partition DateTime de plage uniforme sur une `datetime` colonne de type dans une table lorsque les données ingérées dans la table sont peu susceptibles d’être classées en fonction de cette colonne.

Dans ce cas, il peut être utile de remanier les données entre des étendues afin que chaque extension se termine par des enregistrements d’une plage de temps limitée. Cela entraînera l’efficacité des filtres sur cette `datetime` colonne au moment de la requête.

La fonction de partition utilisée est [bin_at ()](../query/binatfunction.md) et n’est pas personnalisable.

#### <a name="partition-properties"></a>Propriétés de la partition

* `RangeSize``timespan`constante scalaire qui indique la taille de chaque partition DateTime.
  Nous vous recommandons d’utiliser les éléments suivants :
  * Commencez par la valeur `1.00:00:00` (un jour).
  * Ne définissez pas une valeur plus courte, car elle peut entraîner un grand nombre de petites extensions qui ne peuvent pas être fusionnées dans la table.
* `Reference``datetime`constante scalaire qui indique un point fixe dans le temps, selon lequel les partitions DateTime sont alignées.
  * Nous vous recommandons de commencer par `1970-01-01 00:00:00` .
  * S’il existe des enregistrements dans lesquels la clé de partition DateTime a des `null` valeurs, leur valeur de partition est définie sur la valeur de `Reference` .

#### <a name="example"></a>Exemple

L’extrait de code montre une clé de partition de plage DateTime uniforme sur une `datetime` colonne typée nommée `timestamp` .
Il utilise `datetime(1970-01-01)` comme point de référence, avec une taille de `1d` pour chaque partition.

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00"
  }
}
```

## <a name="the-policy-object"></a>Objet de stratégie

Par défaut, la stratégie de partitionnement des données d’une table est `null` , auquel cas les données de la table ne sont pas partitionnées.

La stratégie de partitionnement des données comporte les propriétés principales suivantes :

* **PartitionKeys**:
  * Collection de [clés de partition](#partition-keys) qui définissent la façon dont les données doivent être partitionnées dans la table.
  * Une table peut avoir jusqu’à des `2` clés de partition, avec l’une des options suivantes.
    * Une [clé de partition de hachage](#hash-partition-key).
    * Une [clé de partition DateTime de plage uniforme](#uniform-range-datetime-partition-key).
    * Une [clé de partition de hachage](#hash-partition-key) et une [clé de partition DateTime de plage uniforme](#uniform-range-datetime-partition-key).
  * Chaque clé de partition a les propriétés suivantes :
    * `ColumnName`: `string ` -Nom de la colonne en fonction de laquelle les données seront partitionnées.
    * `Kind`: `string` -Type de partitionnement des données à appliquer ( `Hash` ou `UniformRange` ).
    * `Properties`: `property bag` Définit les paramètres en fonction du partitionnement effectué.

* **EffectiveDateTime**:
  * DateTime UTC à partir de laquelle la stratégie est effective.
  * Cette propriété est facultative. Si elle n’est pas spécifiée, la stratégie prend effet sur les données ingérées après l’application de la stratégie.
  * Toutes les extensions non homogènes (non partitionnées) qui peuvent être supprimées en raison de la rétention sont ignorées par le processus de partitionnement, car leur heure de création précède 90% de la période de suppression effective de la table.

### <a name="example"></a>Exemple

Objet de stratégie de partitionnement de données avec deux clés de partition.
1. Une clé de partition de hachage sur une `string` colonne de type typée nommée `tenant_id` .
    - Elle utilise la `XxHash64` fonction de hachage, avec un `MaxPartitionCount` de 256 et la valeur par défaut `Seed` `1` .
1. Une clé de partition de plage DateTime uniforme sur une `datetime` colonne de type nommée `timestamp` .
    - Il utilise `datetime(1970-01-01)` comme point de référence, avec une taille de `1d` pour chaque partition.

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1
      }
    },
    {
      "ColumnName": "timestamp",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00"
      }
    }
  ]
}
```

### <a name="additional-properties"></a>Propriétés supplémentaires

Les propriétés suivantes peuvent être définies dans le cadre de la stratégie, mais elles sont facultatives et nous vous recommandons de ne pas les modifier.

* **MinRowCountPerOperation**:
  * Cible minimale pour la somme du nombre de lignes des étendues sources d’une opération de partitionnement de données unique.
  * Cette propriété est facultative. Sa valeur par défaut est `0`.

* **MaxRowCountPerOperation**:
  * Cible maximale pour la somme du nombre de lignes des étendues sources d’une opération de partitionnement de données unique.
  * Cette propriété est facultative. Sa valeur par défaut est `0` , avec une cible par défaut de 5 millions enregistrements.
    * Vous pouvez définir une valeur inférieure à 5 millions si vous constatez que les opérations de partitionnement consomment une grande quantité de mémoire ou de processeur, par opération. Pour plus d’informations, consultez [Monitoring](#monitoring).

## <a name="notes"></a>Notes

### <a name="the-data-partitioning-process"></a>Processus de partitionnement des données

* Le partitionnement des données s’exécute en tant que processus d’arrière-plan de publication dans le cluster.
  * Une table qui est ingérée en continu dans est censée avoir toujours une « fin » des données qui doivent être partitionnées (étendues non homogènes).
* Le partitionnement des données s’exécute uniquement sur les extensions chaudes, quelle que soit la valeur de la `EffectiveDateTime` propriété dans la stratégie.
  * Si le partitionnement des extensions à froid est requis, vous devez ajuster temporairement la [stratégie de mise en cache](cachepolicy.md).

#### <a name="monitoring"></a>Surveillance

Utilisez la commande [. afficher les diagnostics](../management/diagnostics.md#show-diagnostics) pour surveiller la progression ou l’état du partitionnement dans un cluster.

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

La sortie comprend les éléments suivants :

  * `MinPartitioningPercentageInSingleTable`: Pourcentage minimal de données partitionnées dans toutes les tables qui ont une stratégie de partitionnement de données dans le cluster.
    * Si ce pourcentage reste constamment inférieur à 90%, évaluez la [capacité](partitioningpolicy.md#capacity)de partitionnement du cluster.
  * `TableWithMinPartitioningPercentage`: Nom qualifié complet de la table dont le pourcentage de partitionnement est indiqué ci-dessus.

Utilisez les [commandes. Show](commands.md) pour surveiller les commandes de partitionnement et leur utilisation des ressources. Par exemple :

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

#### <a name="capacity"></a>Capacité

* Le processus de partitionnement des données entraîne la création d’extensions supplémentaires. Le cluster peut augmenter progressivement sa [capacité de fusion d’étendues](../management/capacitypolicy.md#extents-merge-capacity), afin que le processus de [fusion des extensions](../management/extents-overview.md) puisse suivre.
* Si le débit d’ingestion est élevé, ou si un nombre suffisant de tables ont une stratégie de partitionnement définie, le cluster peut augmenter progressivement sa [capacité de partition](../management/capacitypolicy.md#extents-partition-capacity), afin que [le processus de partitionnement des étendues](#the-data-partitioning-process) puisse suivre.
* Pour éviter de consommer trop de ressources, ces augmentations dynamiques sont limitées. Vous devrez peut-être les augmenter graduellement et de manière linéaire au-delà de la limite, s’ils sont entièrement utilisés.
  * Si l’augmentation des capacités entraîne une augmentation significative de l’utilisation des ressources du cluster, vous pouvez mettre à l' [up](../../manage-cluster-vertical-scaling.md)échelle le cluster / [out](../../manage-cluster-horizontal-scaling.md), soit manuellement, soit en activant la mise à l’échelle automatique.

### <a name="outliers-in-partitioned-columns"></a>Valeurs hors norme dans les colonnes partitionnées

* Si une clé de partition de hachage comprend des valeurs qui sont bien plus fréquentes que d’autres, par exemple une chaîne vide ou une valeur générique (telle que `null` ou `N/A` ), ou qu’elles représentent une entité (telle que `tenant_id` ) qui est plus répandue dans le jeu de données, cela peut contribuer à la distribution déséquilibrée des données sur les nœuds du cluster et nuire aux performances des requêtes
* Si une clé de partition DateTime de plage uniforme a un pourcentage suffisamment élevé de valeurs qui sont « éloignées » de la majorité des valeurs de la colonne, par exemple, les valeurs DateTime du passé ou futur distant, cela peut augmenter la surcharge du processus de partitionnement des données et mener à de nombreuses petites extensions dont le cluster devra effectuer le suivi.

Dans ces deux cas, vous devez soit « corriger » les données, soit filtrer les enregistrements non pertinents dans les données avant ou au moment de l’ingestion, afin de réduire la charge du partitionnement des données sur le cluster. Par exemple, utilisez une [stratégie de mise à jour](updatepolicy.md).

## <a name="next-steps"></a>Étapes suivantes

Utilisez les [commandes de contrôle de la stratégie de partitionnement](../management/partitioning-policy.md) pour gérer les stratégies de partitionnement des données pour les tables.
