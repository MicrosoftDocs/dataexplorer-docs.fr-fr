---
title: Stratégie de partitionnement des données-Azure Explorateur de données
description: Cet article décrit la stratégie de partitionnement des données dans Azure Explorateur de données, et comment l’utiliser pour améliorer les performances des requêtes.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: 30929e63c39be10d066815333ba6b277c0aeb5c9
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321282"
---
# <a name="partitioning-policy"></a>Stratégie de partitionnement

La stratégie de partitionnement définit si et comment les [étendues (partitions de données)](../management/extents-overview.md) doivent être partitionnées pour une table spécifique.

L’objectif principal de la stratégie est d’améliorer les performances des requêtes qui réduisent le jeu de données ; Par exemple, les requêtes qui filtrent sur des colonnes partitionnées, utilisent un agrégat ou une jointure sur une colonne de chaîne de cardinalité élevée. La stratégie peut également entraîner une meilleure compression des données.

> [!CAUTION]
> Aucune limite codée en dur n’est définie sur le nombre de tables avec la stratégie de partitionnement définie. Toutefois, chaque table supplémentaire ajoute une surcharge au processus de partitionnement des données en arrière-plan qui s’exécute sur les nœuds du cluster. L’ajout de tables peut entraîner l’utilisation de plus de ressources de cluster. Pour plus d’informations, consultez [surveillance](#monitor-partitioning) et [capacité](#partition-capacity).

## <a name="partition-keys"></a>Clés de partition

Les types de clés de partition suivants sont pris en charge.

|Kind                                                   |Type de colonne |Propriétés de la partition                                               |Valeur de partition                                        |
|-------------------------------------------------------|------------|-------------------------------------------------------------------|-------------------------------------------------------|
|[Hachage](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed`, `PartitionAssignmentMode` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[Plage uniforme](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`, `OverrideCreationTime`                   | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>Clé de partition de hachage

> [!NOTE]
> Appliquez une clé de partition de hachage à une `string` colonne de type dans une table uniquement dans les cas suivants :
> * Si la majorité des requêtes utilisent des filtres d’égalité ( `==` , `in()` ).
> * La plupart des requêtes sont agrégées/jointes sur une `string` colonne de *grande dimension* (cardinalité de 10 millions ou supérieure), telle qu’un `application_ID` , un `tenant_ID` ou un `user_ID` .

* Une fonction de hachage-modulo est utilisée pour partitionner les données.
* Les données dans des étendues homogènes (partitionnées) sont classées par la clé de partition de hachage.
  * Vous n’avez pas besoin d’inclure la clé de partition de hachage dans la [stratégie d’ordre des lignes](roworderpolicy.md), si celle-ci est définie sur la table.
* Les requêtes qui utilisent la [stratégie de lecture aléatoire](../query/shufflequery.md), et dans laquelle le `shuffle key` utilisé dans `join` `summarize` ou `make-series` est la clé de partition de hachage de la table, sont censées fonctionner mieux, car la quantité de données requises pour le déplacement entre les nœuds de cluster est réduite.

#### <a name="partition-properties"></a>Propriétés de la partition

|Propriété | Description | Valeur (s) prise en charge| Valeur recommandée |
|---|---|---|---|
| `Function` | Nom d’une fonction de hachage-modulo à utiliser.| `XxHash64` | |
| `MaxPartitionCount` | Nombre maximal de partitions à créer (l’argument modulo de la fonction de hachage-modulo) par période. | Dans la plage `(1,2048]` . <br>  Plus de cinq fois le nombre de nœuds dans le cluster et plus petit que la cardinalité de la colonne. |  Des valeurs plus élevées entraînent une plus grande surcharge du processus de partitionnement des données sur les nœuds du cluster et un plus grand nombre d’étendues pour chaque période. Pour les clusters contenant moins de 50 nœuds, commencez par `256` . Ajustez la valeur en fonction de ces considérations, ou en fonction de l’avantage des performances des requêtes et de la surcharge liée au partitionnement de la publication des données.
| `Seed` | Utilisez pour la randomisation de la valeur de hachage. | Entier positif. | `1`, qui est également la valeur par défaut. |
| `PartitionAssignmentMode` | Mode utilisé pour assigner des partitions à des nœuds dans le cluster. | `Default`: Toutes les étendues homogènes (partitionnées) appartenant à la même partition sont affectées au même nœud. <br> `Uniform`: Les valeurs de partition d’étendues sont ignorées. Les étendues sont assignées uniformément aux nœuds du cluster. | Si les requêtes ne sont pas jointes ou agrégées sur la clé de partition de hachage, utilisez `Uniform` . Sinon, utilisez `Default`. |

#### <a name="hash-partition-key-example"></a>Exemple de clé de partition de hachage

Une clé de partition de hachage sur une `string` colonne de type typée nommée `tenant_id` .
Elle utilise la `XxHash64` fonction de hachage, avec un `MaxPartitionCount` de `256` et la valeur par défaut `Seed` de `1` .

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1,
    "PartitionAssignmentMode": "Default"
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>Clé de partition DateTime de plage uniforme

> [!NOTE] 
> Appliquez uniquement une clé de partition DateTime de plage uniforme sur une `datetime` colonne de type dans une table lorsque les données ingérées dans la table sont peu susceptibles d’être classées en fonction de cette colonne.

Dans ce cas, vous pouvez remanier les données entre des étendues afin que chaque extension comprenne des enregistrements d’une plage de temps limitée. Ce processus permet d’obtenir des filtres sur la `datetime` colonne plus efficace au moment de la requête.

La fonction de partition utilisée est [bin_at ()](../query/binatfunction.md) et n’est pas personnalisable.

#### <a name="partition-properties"></a>Propriétés de la partition

|Propriété                | Description                                                                                                                                                     | Valeur recommandée                                                                                                                                                                                                                                                            |
|------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `RangeSize`            | `timespan`Constante scalaire qui indique la taille de chaque partition DateTime.                                                                                | Commencez par la valeur `1.00:00:00` (un jour). Ne définissez pas une valeur plus courte, car elle peut entraîner un grand nombre de petites extensions qui ne peuvent pas être fusionnées dans la table.                                                                                                      |
| `Reference`            | `datetime`Constante scalaire qui indique un point fixe dans le temps, selon lequel les partitions DateTime sont alignées.                                          | Commencez par `1970-01-01 00:00:00`. S’il existe des enregistrements dans lesquels la clé de partition DateTime a des `null` valeurs, leur valeur de partition est définie sur la valeur de `Reference` .                                                                                                      |
| `OverrideCreationTime` | Valeur `bool` qui indique si les heures de création minimale et maximale de l’étendue du résultat doivent être remplacées par la plage des valeurs de la clé de partition. | La valeur par défaut est `false`. Affectez `true` la valeur si les données ne sont pas ingérées au moment de l’arrivée (par exemple, un fichier source unique peut inclure des valeurs DateTime qui sont très éloignées) et/ou si vous souhaitez forcer la rétention/la mise en cache en fonction des valeurs DateTime, et non le moment de l’ingestion. |

#### <a name="uniform-range-datetime-partition-example"></a>Exemple de partition DateTime de plage uniforme

L’extrait de code montre une clé de partition de plage DateTime uniforme sur une `datetime` colonne typée nommée `timestamp` .
Il utilise `datetime(1970-01-01)` comme point de référence, avec une taille de `1d` pour chaque partition et ne remplace pas les durées de création des extensions.

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00",
    "OverrideCreationTime": false
  }
}
```

## <a name="the-policy-object"></a>Objet de stratégie

Par défaut, la stratégie de partitionnement des données d’une table est `null` , auquel cas les données de la table ne sont pas partitionnées.

La stratégie de partitionnement des données comporte les propriétés principales suivantes :

* **PartitionKeys**:
  * Collection de [clés de partition](#partition-keys) qui définissent la façon dont les données doivent être partitionnées dans la table.
  * Une table peut avoir jusqu’à des `2` clés de partition, avec l’une des options suivantes :
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
  * Les extensions non homogènes (non partitionnées) qui peuvent être supprimées en raison de la rétention sont ignorées par le processus de partitionnement. Les étendues sont ignorées, car leur durée de création précède 90% de la période de suppression logicielle effective de la table.
    > [!NOTE]
    > Vous pouvez définir une valeur DateTime dans le passé et partitionner les données déjà ingérées. Toutefois, cette pratique peut augmenter considérablement les ressources utilisées dans le processus de partitionnement. 

### <a name="data-partitioning-example"></a>Exemple de partitionnement de données

Objet de stratégie de partitionnement de données avec deux clés de partition.
1. Une clé de partition de hachage sur une `string` colonne de type typée nommée `tenant_id` .
    * Elle utilise la `XxHash64` fonction de hachage, avec un `MaxPartitionCount` de 256 et la valeur par défaut `Seed` `1` .
1. Une clé de partition de plage DateTime uniforme sur une `datetime` colonne de type nommée `timestamp` .
    * Il utilise `datetime(1970-01-01)` comme point de référence, avec une taille de `1d` pour chaque partition.

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1,
        "PartitionAssignmentMode": "Default"
      }
    },
    {
      "ColumnName": "timestamp",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00",
        "OverrideCreationTime": false
      }
    }
  ]
}
```

## <a name="additional-properties"></a>Propriétés supplémentaires

Les propriétés suivantes peuvent être définies dans le cadre de la stratégie. Ces propriétés sont facultatives et nous vous recommandons de ne pas les modifier.

|Propriété | Description | Valeur recommandée | Valeur par défaut |
|---|---|---|---|
| **MinRowCountPerOperation** |  Cible minimale pour la somme du nombre de lignes des étendues sources d’une opération de partitionnement de données unique. | | `0` |
| **MaxRowCountPerOperation** |  Cible maximale pour la somme du nombre de lignes des étendues sources d’une opération de partitionnement de données unique. | Définissez une valeur inférieure à 5M si vous constatez que les opérations de partitionnement consomment une grande quantité de mémoire ou de processeur par opération. Pour plus d’informations, consultez [Monitoring](#monitor-partitioning). | `0`, avec une cible par défaut de 5 millions enregistrements. |

## <a name="the-data-partitioning-process"></a>Processus de partitionnement des données

* Le partitionnement des données s’exécute en tant que processus d’arrière-plan de publication dans le cluster.
  * Une table qui est ingérée en continu dans est censée avoir toujours une « fin » des données qui doivent être partitionnées (étendues non homogènes).
* Le partitionnement des données s’exécute uniquement sur les extensions chaudes, quelle que soit la valeur de la `EffectiveDateTime` propriété dans la stratégie.
  * Si le partitionnement des extensions à froid est requis, vous devez ajuster temporairement la [stratégie de mise en cache](cachepolicy.md).

## <a name="monitor-partitioning"></a>Surveiller le partitionnement

Utilisez la [`.show diagnostics`](../management/diagnostics.md#show-diagnostics) commande pour surveiller la progression ou l’état du partitionnement dans un cluster.

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

La sortie comprend les éléments suivants :

  * `MinPartitioningPercentageInSingleTable`: Pourcentage minimal de données partitionnées dans toutes les tables qui ont une stratégie de partitionnement de données dans le cluster.
    * Si ce pourcentage reste constamment inférieur à 90%, évaluez la [capacité](partitioningpolicy.md#partition-capacity)de partitionnement du cluster.
  * `TableWithMinPartitioningPercentage`: Nom qualifié complet de la table dont le pourcentage de partitionnement est indiqué ci-dessus.

Utilisez [`.show commands`](commands.md) pour surveiller les commandes de partitionnement et leur utilisation des ressources. Par exemple :

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

## <a name="partition-capacity"></a>Capacité de la partition

* Le processus de partitionnement des données entraîne la création d’extensions supplémentaires. Le cluster peut augmenter progressivement sa [capacité de fusion d’étendues](../management/capacitypolicy.md#extents-merge-capacity), afin que le processus de [fusion des extensions](../management/extents-overview.md) puisse suivre.
* Si le débit d’ingestion est élevé, ou si un nombre suffisant de tables ont une stratégie de partitionnement définie, le cluster peut augmenter progressivement sa [capacité de partition](../management/capacitypolicy.md#extents-partition-capacity), afin que [le processus de partitionnement des étendues](#the-data-partitioning-process) puisse suivre.
* Pour éviter de consommer trop de ressources, ces augmentations dynamiques sont limitées. Vous devrez peut-être les augmenter graduellement et de manière linéaire au-delà de la limite, s’ils sont entièrement utilisés.
  * Si l’augmentation des capacités entraîne une augmentation significative de l’utilisation des ressources du cluster, vous pouvez mettre à l' [up](../../manage-cluster-vertical-scaling.md)échelle le cluster / [out](../../manage-cluster-horizontal-scaling.md), soit manuellement, soit en activant la mise à l’échelle automatique.

## <a name="outliers-in-partitioned-columns"></a>Valeurs hors norme dans les colonnes partitionnées

* Les situations suivantes peuvent contribuer à la distribution déséquilibrée des données sur les nœuds du cluster et à la dégradation des performances des requêtes :
    * Si une clé de partition de hachage comprend des valeurs qui sont bien plus fréquentes que d’autres, par exemple une chaîne vide ou une valeur générique (telle que `null` ou `N/A` ).
    * Les valeurs représentent une entité (telle que `tenant_id` ) qui est plus répandue dans le jeu de données.
* Si une clé de partition DateTime de plage uniforme a un pourcentage suffisamment élevé de valeurs qui sont « éloignées » de la majorité des valeurs de la colonne, la surcharge du processus de partitionnement des données est augmentée et peut entraîner de nombreuses petites extensions dont le cluster devra effectuer le suivi. C’est le cas, par exemple, des valeurs DateTime du passé ou du futur.

Dans ces deux cas, « corrigez » les données, ou filtrez les enregistrements non pertinents dans les données avant ou au moment de l’ingestion, afin de réduire la charge du partitionnement des données sur le cluster. Par exemple, utilisez une [stratégie de mise à jour](updatepolicy.md).

## <a name="next-steps"></a>Étapes suivantes

Utilisez les [commandes de contrôle de la stratégie de partitionnement](../management/partitioning-policy.md) pour gérer les stratégies de partitionnement des données pour les tables.
