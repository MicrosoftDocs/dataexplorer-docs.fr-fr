---
title: Politique cache - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de Cache dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: ca14703b2548bdb23dc3e6e352aeaacbc17303b4
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744473"
---
# <a name="cache-policy"></a>Stratégie de cache

Cet article décrit les commandes utilisées pour la création et la modification de la politique de [cache](cachepolicy.md) 

## <a name="displaying-the-cache-policy"></a>Affichage de la politique de cache

La stratégie peut être définie sur une donnée ou une table, et est affichée en utilisant l’une des commandes suivantes :

* `.show``database` *DatabaseName (en)* `policy``caching`
* `.show``table` *DatabaseName*`.`*TableName* `policy``caching`

## <a name="altering-the-cache-policy"></a>Modification de la politique de cache

```kusto
.alter <entity_type> <database_or_table_name> policy caching hot = <timespan>
```

Modification de la politique de cache pour plusieurs tables (dans le même contexte de base de données) :

```kusto
.alter tables (table_name [, ...]) policy caching hot = <timespan>
```

Politique de cache :

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

* `entity_type`: table, base de données ou cluster
* `database_or_table`: si l’entité est table ou base de données, son nom doit être spécifié dans la commande comme suit - 
  - `database_name` ou 
  - `database_name.table_name` ou 
  - `table_name`(lorsqu’elle est en cours d’exécution dans le contexte de la base de données spécifique)

## <a name="deleting-the-cache-policy"></a>Suppression de la politique de cache

```kusto
.delete <entity_type> <database_or_table_name> policy caching
```

**Exemples**

Afficher la politique `MyTable` de `MyDatabase`cache pour la table dans la base de données :

```kusto
.show table MyDatabase.MyTable policy caching 
```

Définir la politique `MyTable` de dépôt de cache (dans le contexte de la base de données) à 3 jours :

```kusto
.alter table MyTable policy caching hot = 3d
```

Définir la politique pour plusieurs tables (dans le contexte de la base de données), à 3 jours :

```kusto
.alter tables (MyTable1, MyTable2, MyTable3) policy caching hot = 3d
```

Suppression d’une politique sur une table :

```kusto
.delete table MyTable policy caching
```

Suppression d’une politique définie sur une base de données :

```kusto
.delete database MyDatabase policy caching
```
