---
title: La stratégie de rétention Kusto contrôle la manière dont les données sont supprimées-Azure Explorateur de données
description: Cet article décrit la stratégie de rétention dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 3d854262a2a446f983f60c49a5c0ca02f6aa2ffe
ms.sourcegitcommit: 283cce0e7635a2d8ca77543f297a3345a5201395
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84011363"
---
# <a name="retention-policy"></a>Stratégie de rétention

La stratégie de rétention contrôle le mécanisme par lequel les données sont supprimées automatiquement des tables.
Cette suppression est généralement utile pour les données qui circulent dans une table en continu dont la pertinence est basée sur l’âge. Par exemple, la stratégie de rétention peut être utilisée pour une table qui contient des événements de diagnostic qui peuvent devenir inintéressants au bout de deux semaines.

La stratégie de rétention peut être configurée pour une table spécifique ou pour une base de données entière (auquel cas elle s’applique à toutes les tables de la base de données qui ne remplacent pas la stratégie).

La configuration d’une stratégie de rétention est importante pour les clusters qui ingèrent en continu des données, ce qui limite les coûts.

Les données « en dehors » de la stratégie de rétention peuvent être supprimées. Kusto ne garantit pas le moment où la suppression se produit (par conséquent, les données peuvent être « en veille » même si la stratégie de rétention a été déclenchée).

La stratégie de rétention est généralement définie pour limiter l’ancienneté des données depuis la réception (voir [SoftDeletePeriod](#the-policy-object), ci-dessous).

> [!NOTE]
> * L’heure de suppression est imprécise. Le système garantit que les données ne seront pas supprimées avant que la limite soit dépassée, mais la suppression n’est pas immédiate après ce point.
> * Une période de suppression réversible de 0 peut être définie dans le cadre d’une stratégie de rétention au niveau de la table (mais pas dans le cadre d’une stratégie de rétention au niveau de la base de données).
>   * Une fois cette opération effectuée, les données ingérées ne sont pas validées dans la table source, ce qui évite d’avoir à conserver les données. Par conséquent, `Recoverability` ne peut avoir que la valeur `Disabled` . 
>   * Une telle configuration est utile principalement lorsque les données sont ingérées dans une table.
>   Une [stratégie de mise à jour](updatepolicy.md) transactionnelle est utilisée pour la transformer et rediriger la sortie vers une autre table.

## <a name="the-policy-object"></a>Objet de stratégie

Une stratégie de rétention comprend les propriétés suivantes :

* **SoftDeletePeriod**:
    * Intervalle de temps pour lequel il est garanti que les données sont conservées disponibles pour la requête, mesurées depuis le moment où elles ont été ingérées.
    * La valeur par défaut est `100 years`.
    * Lors de la modification de la période de suppression réversible d’une table ou d’une base de données, la nouvelle valeur s’applique à la fois aux données existantes et nouvelles.
* **Récupération**:
    * Récupération de données (activée/désactivée) après la suppression des données
    * La valeur par défaut est `Enabled`
    * Si la valeur est définie sur `Enabled` , les données seront récupérables pendant 14 jours après avoir été supprimées de manière réversible.

## <a name="control-commands"></a>Commandes de contrôle

* Utilisez [. afficher la rétention](../management/retention-policy.md) de la stratégie pour afficher la stratégie de rétention actuelle d’une base de données ou d’une table.
* Utilisez la [rétention](../management/retention-policy.md) de la stratégie pour modifier la stratégie de rétention actuelle d’une base de données ou d’une table.

## <a name="defaults"></a>Valeurs par défaut

Par défaut, lorsqu’une base de données ou une table est créée, aucune stratégie de rétention n’est définie.
Dans les cas courants, la base de données est créée et sa stratégie de rétention est immédiatement définie par son créateur conformément aux exigences connues.
Lors de l’exécution d’une [commande show](../management/retention-policy.md) pour la stratégie de rétention d’une base de données ou d’une table dont la stratégie n’a pas été définie, `Policy` s’affiche sous la forme `null` .

La stratégie de rétention par défaut (avec les valeurs par défaut mentionnées ci-dessus) peut être appliquée à l’aide de la commande suivante :

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

Ces résultats sont appliqués avec l’objet de stratégie suivant appliqué à la base de données ou à la table :

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

L’effacement de la stratégie de rétention d’une base de données ou d’une table peut être effectué à l’aide de la commande suivante :

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>Exemples

Étant donné que votre cluster a une base de données nommée `MyDatabase` , avec des tables `MyTable1` `MyTable2` et`MySpecialTable`

**1. définition de toutes les tables de la base de données pour qu’elles aient une période de suppression réversible de 7 jours et une récupération désactivée**:

* *Option 1 (recommandée)*: définissez une stratégie de rétention au niveau de la base de données avec une période de suppression réversible de sept jours et une capacité de récupération désactivée, et vérifiez qu’aucune stratégie de niveau table n’est définie.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *Option 2*: pour chaque table, définissez une stratégie de rétention au niveau de la table avec une période de suppression réversible de sept jours et une récupération désactivée.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. définition de tables `MyTable1` , `MyTable2` pour avoir une période de suppression réversible de 7 jours et une capacité de récupération activée, et définie `MySpecialTable` pour avoir une période de suppression réversible de 14 jours et une capacité de récupération désactivée**:

* *Option 1 (recommandée)*: définissez une stratégie de rétention au niveau de la base de données avec une période de suppression réversible de sept jours et une récupération activée, puis définissez une stratégie de rétention de niveau table avec une période de suppression réversible de 14 jours et une capacité de récupération désactivée pour `MySpecialTable` .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *Option 2*: pour chaque table, définissez une stratégie de rétention au niveau de la table avec la période de suppression réversible souhaitée et la capacité de récupération.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. définition de tables `MyTable1` , `MyTable2` pour avoir une période de suppression réversible de 7 jours et `MySpecialTable` conserver ses données indéfiniment**:

* *Option 1*: définissez une stratégie de rétention au niveau de la base de données avec une période de suppression réversible de sept jours et définissez une stratégie de rétention au niveau de la table avec une période de suppression réversible de 100 ans (la stratégie de rétention par défaut) pour `MySpecialTable` .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *Option 2*: pour les tables `MyTable1` , `MyTable2` , définissez une stratégie de rétention au niveau de la table avec la période de suppression réversible souhaitée de sept jours et vérifiez que la stratégie au niveau de la base de données et de la table `MySpecialTable` n’est pas définie.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *Option 3*: pour les tables `MyTable1` , `MyTable2` , définissez une stratégie de rétention au niveau de la table avec la période de suppression réversible souhaitée de sept jours. Pour table `MySpecialTable` , définissez une stratégie de rétention au niveau de la table avec une période de suppression réversible de 100 ans (la stratégie de rétention par défaut).

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```
