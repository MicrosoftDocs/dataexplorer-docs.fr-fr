---
title: La stratégie de rétention Kusto contrôle la manière dont les données sont supprimées-Azure Explorateur de données
description: Cet article décrit les stratégies de rétention dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 37d82807751a604d88eda7de75fb4978efc0ce1b
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321265"
---
# <a name="retention-policy"></a>Stratégie de rétention

La stratégie de rétention contrôle le mécanisme qui supprime automatiquement les données des tables ou des [vues matérialisées](materialized-views/materialized-view-overview.md). Il est utile de supprimer les données qui sont transmises en continu à une table, dont la pertinence est basée sur l’âge. Par exemple, la stratégie peut être utilisée pour une table qui contient des événements de diagnostic qui peuvent devenir inintéressants au bout de deux semaines.

La stratégie de rétention peut être configurée pour une table ou une vue matérialisée spécifique, ou pour une base de données entière. La stratégie s’applique alors à toutes les tables de la base de données qui ne se substituent pas à celle-ci.

La configuration d’une stratégie de rétention est importante pour les clusters qui ingèrent en continu des données, ce qui limite les coûts.

Les données « en dehors » de la stratégie de rétention peuvent être supprimées. Il n’existe aucune garantie spécifique lors de la suppression. Les données peuvent être en « Lingo » même si la stratégie de rétention est déclenchée.

La stratégie de rétention est généralement définie pour limiter l’ancienneté des données depuis l’ingestion. Pour plus d’informations, consultez [SoftDeletePeriod](#the-policy-object).

> [!NOTE]
> * L’heure de suppression est imprécise. Le système garantit que les données ne sont pas supprimées avant que la limite soit dépassée, mais la suppression n’est pas immédiate après ce point.
> * Une période de suppression réversible de 0 peut être définie dans le cadre d’une stratégie de rétention au niveau de la table, mais pas dans le cadre d’une stratégie de rétention au niveau de la base de données.
>   * Une fois cette opération effectuée, les données ingérées ne sont pas validées dans la table source, ce qui évite d’avoir à conserver les données. Par conséquent, `Recoverability` ne peut avoir que la valeur `Disabled` .
>   * Une telle configuration est utile principalement lorsque les données sont ingérées dans une table.
> Une [stratégie de mise à jour](updatepolicy.md) transactionnelle est utilisée pour la transformer et rediriger la sortie vers une autre table.

## <a name="the-policy-object"></a>Objet de stratégie

Une stratégie de rétention comprend les propriétés suivantes :

* **SoftDeletePeriod**:
    * Intervalle de temps pendant lequel il est garanti que les données restent disponibles pour la requête. La période est mesurée à partir du moment où les données ont été ingérées.
    * La valeur par défaut est `100 years`.
    * Lors de la modification de la période de suppression réversible d’une table ou d’une base de données, la nouvelle valeur s’applique à la fois aux données existantes et nouvelles.
* **Récupération**:
    * Récupération de données (activée/désactivée) après la suppression des données.
    * La valeur par défaut est `Enabled`.
    * Si la valeur est définie sur `Enabled` , les données seront récupérables pendant 14 jours après avoir été supprimées de manière réversible.

## <a name="control-commands"></a>Commandes de contrôle

* Permet [`.show policy retention`](../management/retention-policy.md) d’afficher la stratégie de rétention actuelle pour une base de données, une table ou une [vue matérialisée](materialized-views/materialized-view-overview.md).
* Utilisez [`.alter policy retention`](../management/retention-policy.md) pour modifier la stratégie de rétention actuelle d’une base de données, d’une table ou d’une [vue matérialisée](materialized-views/materialized-view-overview.md).

## <a name="defaults"></a>Valeurs par défaut

Par défaut, lorsqu’une base de données ou une table est créée, aucune stratégie de rétention n’est définie. Normalement, la base de données est créée et sa stratégie de rétention est immédiatement définie par son créateur conformément aux exigences connues.
Quand vous exécutez une [ `.show` commande](../management/retention-policy.md) pour la stratégie de rétention d’une base de données ou d’une table dont la stratégie n’a pas été définie, `Policy` s’affiche sous la forme `null` .

La stratégie de rétention par défaut, avec les valeurs par défaut mentionnées ci-dessus, peut être appliquée à l’aide de la commande suivante.

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
.alter materialized-view ViewName policy retention "{}"
```

La commande génère l’objet de stratégie suivant appliqué à la base de données ou à la table.

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

L’effacement de la stratégie de rétention d’une base de données ou d’une table peut être effectué à l’aide de la commande suivante.

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>Exemples

Pour un cluster qui a une base de données nommée `MyDatabase` , avec les tables `MyTable1` , `MyTable2` et `MySpecialTable` .

### <a name="soft-delete-period-of-seven-days-and-recoverability-disabled"></a>Période de suppression réversible de sept jours et de récupération désactivée

Définissez toutes les tables de la base de données pour qu’elles aient une période de suppression réversible de sept jours et une récupération désactivée.

* *Option 1 (recommandée)*: définissez une stratégie de rétention au niveau de la base de données et vérifiez qu’il n’y a pas de stratégies au niveau de la table définies.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge materialized-view ViewName policy retention softdelete = 7d 
```

* *Option 2*: pour chaque table, définissez une stratégie de rétention au niveau de la table, avec une période de suppression réversible de sept jours et une récupération désactivée.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

### <a name="soft-delete-period-of-seven-days-and-recoverability-enabled"></a>Période de suppression réversible de sept jours et capacité de récupération activée

* Définir `MyTable1` des tables et `MyTable2` avoir une période de suppression réversible de sept jours et une capacité de récupération activée.
* Défini `MySpecialTable` pour avoir une période de suppression réversible de 14 jours et une capacité de récupération désactivée.

* *Option 1 (recommandée)*: définissez une stratégie de rétention au niveau de la base de données et définissez une stratégie de rétention au niveau de la table.

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *Option 2*: pour chaque table, définissez une stratégie de rétention au niveau de la table, avec la période de suppression réversible appropriée et la capacité de récupération.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

### <a name="soft-delete-period-of-seven-days-and-myspecialtable-keeps-its-data-indefinitely"></a>Période de suppression réversible de sept jours et `MySpecialTable` conservation de ses données indéfiniment

Définir `MyTable1` des tables et `MyTable2` avoir une période de suppression réversible de sept jours et `MySpecialTable` conserver les données indéfiniment.

* *Option 1*: définir une stratégie de rétention au niveau de la base de données et définir une stratégie de rétention au niveau de la table, avec une période de suppression réversible de 100 ans, la stratégie de rétention par défaut, pour `MySpecialTable` .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *Option 2*: pour les tables `MyTable1` et `MyTable2` , définissez une stratégie de rétention au niveau de la table et vérifiez que la stratégie au niveau de la base de données et de la table `MySpecialTable` n’est pas définie.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *Option 3*: pour les tables `MyTable1` et `MyTable2` , définissez une stratégie de rétention au niveau de la table. Pour table `MySpecialTable` , définissez une stratégie de rétention au niveau de la table avec une période de suppression réversible de 100 ans, la stratégie de rétention par défaut.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
