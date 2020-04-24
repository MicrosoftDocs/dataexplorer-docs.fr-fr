---
title: Politique de rétention - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de rétention dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 81e08b6e007a6e3c669e7138e1d36ae1e701d442
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520313"
---
# <a name="retention-policy"></a>Stratégie de rétention

La stratégie de conservation contrôle automatiquement le mécanisme par lequel les données sont supprimées des tables.
Une telle suppression est généralement utile pour les données qui se jet dans un tableau en permanence dont la pertinence est basée sur l’âge. Par exemple, la politique de rétention peut être utilisée pour un tableau qui contient des événements diagnostiques qui peuvent devenir inintéressants après deux semaines.

La stratégie de rétention peut être configurée pour une table spécifique ou pour toute une base de données (auquel cas elle s’applique à toutes les tables de la base de données qui ne remplacent pas la stratégie).

La mise en place d’une politique de rétention est importante pour les grappes qui ingéré continuellement des données, ce qui limitera les coûts.

Les données « extérieures » à la politique de conservation sont admissibles à l’élimination. Kusto ne garantit pas quand la suppression se produit (ainsi les données peuvent « s’attarder » même si la politique de conservation a été déclenchée).

La politique de conservation est le plus souvent définie pour limiter l’âge des données depuis l’ingestion (voir [SoftDeletePeriod](#the-policy-object), ci-dessous).

> [!NOTE]
> * Le temps de suppression est imprécis. Le système garantit que les données ne seront pas supprimées avant que la limite ne soit dépassée, mais la suppression n’est pas immédiate à la suite de ce point.
> * Une période de suppression souple de 0 peut être définie dans le cadre d’une politique de conservation au niveau de la table (mais pas dans le cadre d’une politique de conservation au niveau de la base de données).
>   * Lorsque cela est fait, les données ingérées ne seront pas validées à la table source, évitant ainsi la nécessité de poursuivre les données.
>   * Une telle configuration est utile principalement lorsque les données sont ingérées dans une table.
>   Une stratégie de [mise à jour](updatepolicy.md) transactionnelle est utilisée pour la transformer et rediriger la sortie en un autre tableau.

## <a name="the-policy-object"></a>L’objet de la politique

Une politique de conservation comprend les propriétés suivantes :

* **SoftDeletePeriod**:
    * Un laps de temps pour lequel il est garanti que les données sont conservées disponibles à la requête, mesurée depuis le moment où elles ont été ingérées.
    * La valeur par défaut est `100 years`.
    * Lors de la modification de la période de suppression d’un tableau ou d’une base de données, la nouvelle valeur s’applique à la fois aux données existantes et nouvelles.
* **Récupération :**
    * Récupération des données (activée/désactivée) après la suppression des données
    * La valeur par défaut est `enabled`
    * Si elles `enabled`sont définies, les données seront récupérables pendant 14 jours après la suppression

## <a name="control-commands"></a>Commandes de contrôle

* Utilisez [la conservation de la politique .show](../management/retention-policy.md) pour afficher la politique de conservation actuelle d’une base de données ou d’un tableau.
* Utilisez [la conservation de la stratégie .alter](../management/retention-policy.md) pour modifier la politique de conservation actuelle d’une base de données ou d’un tableau.

## <a name="defaults"></a>Valeurs par défaut

Par défaut, lorsqu’une base de données ou une table est créée, elle n’a pas de stratégie de conservation définie.
Dans les cas courants, la base de données est créée et a immédiatement sa politique de rétention définie par son créateur en fonction des exigences connues.
Lors de l’exécution d’une [commande de spectacle](../management/retention-policy.md) pour la politique `Policy` de `null`conservation d’une base de données ou d’une table qui n’a pas eu son ensemble de politique, apparaît comme .

La stratégie de rétention par défaut (avec les valeurs par défaut mentionnées ci-dessus) peut être appliquée à l’aide de la commande suivante :

```kusto
.alter database DatabaseName policy retention "{}"
.alter table TableName policy retention "{}"
```

Ces résultats avec l’objet de stratégie suivant appliqué à la base de données ou au tableau :

```kusto
{
  "SoftDeletePeriod": "36500.00:00:00", "Recoverability":"Enabled"
}
```

L’élimination de la politique de conservation d’une base de données ou d’une table peut être effectuée à l’aide de la commande suivante :

```kusto
.delete database DatabaseName policy retention
.delete table TableName policy retention
```

## <a name="examples"></a>Exemples

Compte tenu de votre `MyDatabase`cluster `MyTable1`a `MyTable2` une base de données nommée , avec des tables , et`MySpecialTable`

**1. Définir toutes les tables de la base de données pour avoir une période de suppression souple de 7 jours et la récupération désactivée**:

* *Option 1 (Recommandé)*: Définissez une politique de conservation au niveau de la base de données avec une période de suppression souple de sept jours et la récupération désactivée, et vérifiez qu’il n’y a pas de stratégie de niveau de table définie.

```kusto
.delete table MyTable1 policy retention        // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention        // optional, only if the table previously had its policy set
.delete table MySpecialTable policy retention  // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
```

* *Option 2*: Pour chaque table, définissez une politique de rétention au niveau de la table avec une période de suppression souple de sept jours et la récupération désactivée.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 7d recoverability = disabled
```

**2. Réglage `MyTable1` `MyTable2` des tables , pour avoir une période de suppression `MySpecialTable` douce de 7 jours et la récupération activée, et réglé pour avoir une période de suppression douce de 14 jours et la récupération désactivée:**

* *Option 1 (Recommandé)*: Définissez une politique de conservation au niveau de la base de données avec une période de suppression souple de sept `MySpecialTable`jours et la récupération activée, et définissez une politique de rétention au niveau de la table avec une période de suppression de 14 jours et une récupération désactivée pour .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

* *Option 2*: Pour chaque table, définissez une politique de rétention au niveau de la table avec la période de suppression souple et la récupération souhaitées.

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MyTable2 policy retention softdelete = 7d recoverability = disabled
.alter-merge table MySpecialTable policy retention softdelete = 14d recoverability = enabled
```

**3. Réglage `MyTable1` `MyTable2` des tables , pour avoir une période `MySpecialTable` de suppression douce de 7 jours, et ont gardé ses données indéfiniment**:

* *Option 1*: Définissez une politique de conservation au niveau de la base de données avec une période de suppression souple de sept `MySpecialTable`jours, et définissez une politique de conservation au niveau de la table avec une période de suppression de 100 ans (la politique de rétention par défaut) pour .

```kusto
.delete table MyTable1 policy retention   // optional, only if the table previously had its policy set
.delete table MyTable2 policy retention   // optional, only if the table previously had its policy set
.alter-merge database MyDatabase policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}" // this sets the default retention policy
```

* *Option 2*: `MyTable1` `MyTable2`Pour les tables , , définissez une politique de rétention au niveau de la table avec `MySpecialTable` la période de suppression souple souhaitée de sept jours, et vérifiez que la stratégie de base de données et de table pour ne sont pas définies.

```kusto
.delete database MyDatabase policy retention   // optional, only if the database previously had its policy set
.delete table MySpecialTable policy retention   // optional, only if the table previously had its policy set
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
```

* *Option 3*: `MyTable1` `MyTable2`Pour les tables , , définissez une politique de rétention au niveau de la table avec la période de suppression souple souhaitée de sept jours. Pour `MySpecialTable`la table, définissez une politique de conservation au niveau de la table avec une période de suppression souple de 100 ans (la politique de rétention par défaut).

```kusto
.alter-merge table MyTable1 policy retention softdelete = 7d
.alter-merge table MyTable2 policy retention softdelete = 7d
.alter table MySpecialTable policy retention "{}"
```