---
title: Stratégie de partitionnement de données (version préliminaire)-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la stratégie de partitionnement des données (préversion) dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/30/2020
ms.openlocfilehash: ad255c6930e76628a5187fa8d321e3445dbb5f99
ms.sourcegitcommit: fbe298e88542c0dcea0f491bb53ac427f850f729
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82138864"
---
# <a name="data-partitioning-policy-preview"></a>Stratégie de partitionnement des données (préversion)

La stratégie de partitionnement définit si et comment les [étendues (données partitions)](../management/extents-overview.md) doivent être partitionnées pour une table spécifique.

> [!NOTE]
> La fonctionnalité de partitionnement des données est en version *préliminaire*.

L’objectif principal de la stratégie est d’améliorer les performances des requêtes qui sont limitées à un petit sous-ensemble de valeurs dans la ou les colonnes partitionnées.
Un avantage potentiel secondaire est une meilleure compression des données.

> [!WARNING]
> Bien qu’il n’y ait aucune limite codée en dur définie sur la quantité de tables sur lesquelles la stratégie peut être définie, chaque table supplémentaire ajoute une surcharge au processus de partitionnement des données en arrière-plan qui s’exécute sur les nœuds du cluster et peut nécessiter des ressources supplémentaires à partir du cluster-voir [capacité](#capacity).

## <a name="partition-keys"></a>Clés de partition

Les types de clés de partition suivants sont pris en charge :

|Type                                                   |Type de colonne |Propriétés de la partition                    |Valeur de partition                                        |
|-------------------------------------------------------|------------|----------------------------------------|-------------------------------------------------------|
|[Code de hachage](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[Plage uniforme](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>Clé de partition de hachage

L’application d’une clé de partition `string`de hachage sur une colonne typée dans une table est appropriée lorsque la *majorité* des requêtes`==`utilisent `in()`des filtres d’égalité `string`(,) sur une colonne de *grande dimension*, `application_ID`telle `tenant_ID` que `user_ID`, ou.

* Une fonction de hachage-modulo est utilisée pour partitionner les données.
* Toutes les étendues *homogènes* (partitionnées) appartenant à la même partition sont affectées au même nœud de données.
* Les données dans des étendues *homogènes* (partitionnées) sont classées par la clé de partition de hachage.
  * Il n’est pas nécessaire d’inclure la clé de partition dans une [stratégie d’ordre des lignes](roworderpolicy.md), si celle-ci est définie sur la table.
* Les requêtes qui utilisent la [stratégie de lecture aléatoire](../query/shufflequery.md), et `shuffle key` dans lesquelles `join`le `summarize` utilisé `make-series` dans ou est la clé de partition de hachage de la table, sont censés être plus performants, car la quantité de données requise pour le déplacement entre les nœuds de cluster est considérablement réduite.

#### <a name="partition-properties"></a>Propriétés de la partition

* `Function`nom d’une fonction de hachage-modulo à utiliser.
  * Valeur prise en `XxHash64`charge :.
* `MaxPartitionCount`nombre maximal de partitions à créer (l’argument modulo de la fonction de hachage-modulo) par période.
  * Les valeurs prises en charge sont `(1,1024]`comprises dans la plage.
    * La valeur doit être :
      * Plus grand que le nombre de nœuds dans le cluster
      * Plus petite que la cardinalité de la colonne.
    * Plus la valeur est élevée, plus la charge mémoire du processus de partitionnement des données sur les nœuds du cluster est importante, et plus le nombre d’étendues est élevé pour chaque période de temps.
    * La valeur recommandée pour commencer par est `256`.
      * Si nécessaire, il peut être ajusté (généralement vers le haut), en fonction des considérations susmentionnées et/ou en fonction de la mesure de l’avantage des performances des requêtes et de la surcharge liée au partitionnement de la publication des données.
* `Seed`valeur à utiliser pour la randomisation de la valeur de hachage.
  * La valeur doit être un entier positif.
  * La valeur recommandée (et par défaut, si non spécifié) `1`est.

#### <a name="example"></a>Exemple

Voici une clé de partition de hachage sur une `string`colonne de type typée nommée `tenant_id`.

Elle utilise la `XxHash64` fonction de hachage, avec `MaxPartitionCount` un `256`de et la valeur `Seed` par `1`défaut de.

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

L’application d’une clé de partition DateTime de `datetime`plage uniforme sur une colonne typée dans une table est appropriée lorsque les données ingérées dans la table sont *peu susceptibles* d’être classées en fonction de cette colonne. Dans ce cas, il peut être utile de remanier de nouveau les données entre les étendues afin que chaque étendue corresponde à des enregistrements d’un intervalle de temps limité, `datetime` ce qui aboutit à des filtres sur la colonne au moment de la requête plus efficace.

* La fonction de partition utilisée est [bin_at ()](../query/binatfunction.md) et n’est pas personnalisable.

#### <a name="partition-properties"></a>Propriétés de la partition

* `RangeSize`constante `timespan` scalaire qui indique la taille de chaque partition DateTime.
* `Reference`constante `datetime` scalaire de type qui indique un point fixe dans le temps selon lequel les partitions DateTime sont alignées.
  * S’il existe des enregistrements dans lesquels la clé de partition `null` DateTime a des valeurs, leur valeur de partition est définie `Reference`sur la valeur de.

#### <a name="example"></a>Exemple

Voici une clé de partition de plage DateTime uniforme sur une `datetime`colonne typée nommée`timestamp`

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

Par défaut, la stratégie de partitionnement des données d' `null`une table est, auquel cas les données de la table ne sont pas partitionnées.

La stratégie de partitionnement des données comporte les propriétés principales suivantes :

* **PartitionKeys**:
  * Collection de [clés de partition](#partition-keys) qui définissent la façon dont les données doivent être partitionnées dans la table.
  * Une table peut avoir jusqu’à `2` des clés de partition, avec l’une des trois options suivantes :
    * 1 [clé de partition de hachage](#hash-partition-key).
    * 1 [clé de partition DateTime de plage uniforme](#uniform-range-datetime-partition-key).
    * 1 [clé de partition de hachage](#hash-partition-key) et 1 [clé de partition DateTime de plage uniforme](#uniform-range-datetime-partition-key).
  * Chaque clé de partition a les propriétés suivantes :
    * `ColumnName`: `string ` -Nom de la colonne en fonction de laquelle les données seront partitionnées.
    * `Kind`: `string` -Type de partitionnement des données à appliquer`Hash` ( `UniformRange`ou).
    * `Properties`: `property bag` définit les paramètres en fonction du partitionnement effectué.

* **EffectiveDateTime**:
  * DateTime UTC à partir de laquelle la stratégie est effective.
  * Cette propriété est *facultative* . si elle n’est pas spécifiée, la stratégie prend effet sur les données ingérées après l’application de la stratégie.
  * Toutes les étendues non homogènes (non partitionnées) qui sont liées pour être bientôt supprimées en raison de la rétention (autrement dit, leur heure de création précède la 90% de la période de suppression effective de la table) est ignorée par le processus de partitionnement.

### <a name="example"></a>Exemple

Voici un objet de stratégie de partitionnement de données avec deux clés de partition :
1. Une clé de partition de hachage `string`sur une colonne de `tenant_id`type typée nommée.
    - Elle utilise la `XxHash64` fonction de hachage, avec `MaxPartitionCount` un de 256 et la valeur `Seed` par `1`défaut.
2. Une clé de partition de plage DateTime uniforme `datetime`sur une colonne typée nommée`timestamp`
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

Les propriétés suivantes peuvent être définies dans le cadre de la stratégie, mais sont *facultatives* et il n’est pas recommandé de les modifier.

* **MinRowCountPerOperation**:
  * Cible minimale pour la somme du nombre de lignes des étendues sources d’une opération de partitionnement de données unique.
  * Cette propriété est *facultative*, avec sa valeur par défaut `0`.

* **MaxRowCountPerOperation**:
  * Cible maximale pour la somme du nombre de lignes des étendues sources d’une opération de partitionnement de données unique.
  * Cette propriété est *facultative*, avec sa valeur par défaut `0` (auquel cas, une cible par défaut de 5 millions enregistrements est activée).

## <a name="notes"></a>Notes

### <a name="the-data-partitioning-process"></a>Processus de partitionnement des données

* Le partitionnement des données s’exécute en tant que processus d’arrière-plan de publication dans le cluster.
  * Une table qui est ingérée en permanence dans est censée avoir toujours une « fin » de données qui doit encore être partitionnée (étendues*non homogènes* ).
* Le partitionnement des données s’exécute uniquement sur les extensions chaudes, quelle que soit `EffectiveDateTime` la valeur de la propriété dans la stratégie.
  * Si le partitionnement des extensions à froid est nécessaire, vous devez ajuster temporairement la [stratégie de mise en cache en](cachepolicy.md) conséquence.

#### <a name="monitoring"></a>Surveillance

* Vous pouvez surveiller la progression/l’état du partitionnement dans un cluster à l’aide de la commande [. afficher les diagnostics](../management/diagnostics.md#show-diagnostics) :

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable,
          TableWithMinPartitioningPercentage
```

La sortie comprend les éléments suivants :

  * `MinPartitioningPercentageInSingleTable`: pourcentage minimal de données partitionnées dans toutes les tables qui ont une stratégie de partitionnement de données dans le cluster.
      * Si ce pourcentage reste constamment inférieur à 90%, évaluez la capacité de partitionnement du cluster (voir [ci-dessous](partitioningpolicy.md#capacity)).
  * `TableWithMinPartitioningPercentage`: nom qualifié complet de la table dont le pourcentage de partitionnement est indiqué ci-dessus.

#### <a name="capacity"></a>Capacité

* Au fur et à mesure que le processus de partitionnement des données entraîne la création d’extensions supplémentaires, vous devrez peut-être augmenter la [capacité de fusion des étendues](../management/capacitypolicy.md#extents-merge-capacity) du cluster pour que le processus de fusion d' [étendues](../management/extents-overview.md) puisse continuer.
* Si elle est requise (par exemple, en cas de débit d’ingestion élevé et/ou d’un nombre suffisant de tables nécessitant un partitionnement), la capacité de la [partition](../management/capacitypolicy.md#extents-partition-capacity) du cluster peut être augmentée (progressivement et linéaire) pour permettre l’exécution d’un plus grand nombre d’opérations de partitionnement simultanées.
  * Si l’augmentation du partitionnement entraîne une augmentation significative de l’utilisation des ressources du cluster, augmentez ou diminuez la taille du cluster, soit manuellement, soit en activant la mise à l’échelle automatique.

### <a name="outliers-in-partitioned-columns"></a>Valeurs hors norme dans les colonnes partitionnées

* Si une clé de partition de hachage a un pourcentage suffisamment élevé de valeurs qui ne sont pas remplies correctement (par exemple, elles sont vides ou ont une valeur générique), cela peut contribuer à avoir une distribution non équilibrée des données sur les nœuds du cluster.
* Si une clé de partition DateTime de plage uniforme a un pourcentage suffisamment élevé de valeurs qui sont « éloignées » de la majorité des valeurs de la colonne (par exemple, les valeurs DateTime du passé ou futur distant).

Dans ces deux cas, vous devez soit « corriger » les données, soit filtrer les enregistrements non pertinents dans les données avant ou au moment de l’ingestion (par exemple, à l’aide d’une [stratégie de mise à jour](updatepolicy.md)), afin de réduire la surcharge liée au partitionnement des données sur le cluster.

## <a name="next-steps"></a>Étapes suivantes

Utilisez les [commandes de contrôle de la stratégie de partitionnement](../management/partitioning-policy.md) pour gérer les stratégies de partitionnement des données pour les tables.
