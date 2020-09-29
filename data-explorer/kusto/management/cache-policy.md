---
title: Stratégie de cache-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la stratégie de cache dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 9b080badd2dc1015319e9b6d44c4c477061f92f9
ms.sourcegitcommit: 041272af91ebe53a5d573e9902594b09991aedf0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91452712"
---
# <a name="cache-policy-command"></a>Commande de stratégie de cache

Cet article décrit les commandes utilisées pour la création et la modification de la [stratégie de cache](cachepolicy.md) 

## <a name="displaying-the-cache-policy"></a>Affichage de la stratégie de cache

La stratégie peut être définie sur une base de données, une table ou une [vue matérialisée](materialized-views/materialized-view-overview.md), et elle est affichée à l’aide de l’une des commandes suivantes :

* `.show``database` *DatabaseName* DatabaseName `policy``caching`
* `.show``table` *TableName* TableName `policy``caching`
* `.show``materialized-view` *MaterializedViewName* MaterializedViewName `policy``caching`

## <a name="altering-the-cache-policy"></a>Modification de la stratégie de cache

```kusto
.alter <entity_type> <database_or_table_or_materialized-view_name> policy caching hot = <timespan>
```

Modification de la stratégie de cache pour plusieurs tables (dans le même contexte de base de données) :

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

Stratégie de cache :

```kusto
{
  "DataHotSpan": {
    "Value": "3.00:00:00"
  },
  "IndexHotSpan": {
    "Value": "3.00:00:00"
  }
}
```

* `entity_type` : table, base de données ou cluster
* `database_or_table_or_materialized-view`: si l’entité est une table ou une base de données, son nom doit être spécifié dans la commande comme suit : 
  - `database_name` ou 
  - `database_name.table_name` ou 
  - `table_name` (en cas d’exécution dans le contexte de la base de données spécifique)

## <a name="deleting-the-cache-policy"></a>Suppression de la stratégie de cache

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**Exemples**

Afficher la stratégie de cache pour la table `MyTable` dans la base de données `MyDatabase` :

```kusto
.show table MyDatabase.MyTable policy caching 
```

Définition de la stratégie de cache de la table `MyTable` (dans le contexte de base de données) sur 3 jours :

```kusto
.alter table MyTable policy caching hot = 3d
.alter materialized-view MyMaterializedView policy caching hot = 3d
```

Définition de la stratégie pour plusieurs tables (dans le contexte de base de données), à 3 jours :

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

Suppression d’une stratégie définie sur une table :

```kusto
.delete table MyTable policy caching
```

Suppression d’une stratégie définie sur une vue matérialisée :

```kusto
.delete materialized-view MyMaterializedView policy caching
```

Suppression d’une stratégie définie sur une base de données :

```kusto
.delete database MyDatabase policy caching
```
