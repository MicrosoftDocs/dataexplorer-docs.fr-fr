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
ms.openlocfilehash: b0366bef619d815bbe58f91730eff70ec847c239
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520347"
---
# <a name="retention-policy"></a>Stratégie de rétention

Cet article décrit les commandes de contrôle utilisées pour créer et modifier la [politique de rétention](retentionpolicy.md).

## <a name="show-retention-policy"></a>Afficher la politique de rétention

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`: table ou base de données
* `database_or_table`: `database_name` `database_name.table_name` ou `table_name` (dans le contexte de la base de données)

**Exemple**

Afficher la politique de `MyDatabase`conservation de la base de données nommée :

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>Supprimer la politique de rétention

La suppression de la politique de conservation des données est un établissement affectif de la conservation illimitée des données.

La suppression de la politique de conservation des données de la table entraînera la suppression de la politique de conservation de la base de données.

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`: table ou base de données
* `database_or_table`: `database_name` `database_name.table_name` ou `table_name` (dans le contexte de la base de données)

**Exemple**

Supprimer la stratégie de `MyTable1`rétention pour le tableau nommé :

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>Modifier la politique de rétention

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`: table ou base de données
* `database_or_table`: `database_name` `database_name.table_name` ou `table_name` (dans le contexte de la base de données)
* `table_name`: nom d’un tableau dans un contexte de base de données.  Une wildcard`*` (est autorisée ici).
* `retention_policy` :

```
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**Exemples**

Afficher la politique de `MyDatabase`conservation de la base de données nommée :

```kusto
.show database MyDatabase policy retention
```

Définit une politique de conservation avec une période de suppression souple de 10 jours et une récupération de données désactivée :

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

Définit une stratégie de conservation avec une période de suppression souple de 10 jours et la récupération des données activée :

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Définit la même politique de rétention que ci-dessus, mais cette fois pour plusieurs tableaux (tableau1, tableau2 et tableau3) :

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Définit une politique de rétention avec les valeurs par défaut : 100 ans au fur et à mesure que la période de suppression et la récupération sont activées :

```kusto
.alter table Table1 policy retention "{}"
```