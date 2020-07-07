---
title: Gestion de la stratégie de rétention Kusto-Azure Explorateur de données
description: Cet article décrit la stratégie de rétention dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ebbd9aa5544d97ef1e980bcb3a53f74dbde66547
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967534"
---
# <a name="retention-policy-command"></a>Commande sur la stratégie de rétention

Cet article décrit les commandes de contrôle utilisées pour la création et la modification de la [stratégie de rétention](retentionpolicy.md).

## <a name="show-retention-policy"></a>Afficher la stratégie de rétention

```kusto
.show <entity_type> <database_or_table> policy retention

.show <entity_type> *  policy retention
```

* `entity_type`: table ou base de données
* `database_or_table`: `database_name` ou `database_name.table_name` ou `table_name` (dans le contexte de base de données)

**Exemple**

Affichez la stratégie de rétention pour la base de données nommée `MyDatabase` :

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>Supprimer la stratégie de rétention

La suppression de la stratégie de rétention des données est affectively paramètre de conservation illimitée des données.

La suppression de la stratégie de rétention des données de la table entraîne la dérive de la stratégie de rétention du niveau de la base de données.

```kusto
.delete <entity_type> <database_or_table> policy retention
```

* `entity_type`: table ou base de données
* `database_or_table`: `database_name` ou `database_name.table_name` ou `table_name` (dans le contexte de base de données)

**Exemple**

Supprimez la stratégie de rétention pour la table nommée `MyTable1` :

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>Modifier la stratégie de rétention

```kusto
.alter <entity_type> <database_or_table> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_name> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type`: table ou base de données
* `database_or_table`: `database_name` ou `database_name.table_name` ou `table_name` (dans le contexte de base de données)
* `table_name`: nom d’une table dans un contexte de base de données.  Caractère générique ( `*` est autorisé ici).
* `retention_policy` :

```kusto
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**Exemples**

Affichez la stratégie de rétention pour la base de données nommée `MyDatabase` :

```kusto
.show database MyDatabase policy retention
```

Définit une stratégie de rétention avec une période de suppression réversible de 10 jours et une récupération des données désactivée :

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled
```

Définit une stratégie de rétention avec une période de suppression réversible de 10 jours et une récupération des données activée :

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Définit la même stratégie de rétention comme indiqué ci-dessus, mais cette fois pour plusieurs tables (Table1, table2 et table3) :

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

Définit une stratégie de rétention avec les valeurs par défaut : 100 ans, car la période de suppression réversible et la récupérabilité sont activées :

```kusto
.alter table Table1 policy retention "{}"
```