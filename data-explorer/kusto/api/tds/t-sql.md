---
title: T-SQL-Azure Explorateur de données
description: Cet article décrit T-SQL dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1115414358a1025d4931484b81d6eda76109e6cd
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373529"
---
# <a name="t-sql-support"></a>Prise en charge de T-SQL

Le langage [de requête Kusto (KQL)](../../query/index.md) est le langage de requête par défaut.
Toutefois, la prise en charge de T-SQL est utile pour les outils qui ne peuvent pas être facilement convertis pour utiliser KQL.  
La prise en charge de T-SQL est également utile pour les personnes connaissant SQL.

Kusto peut interpréter et exécuter des requêtes T-SQL avec certaines limitations du langage.

> [!NOTE]
> Kusto ne prend pas en charge les commandes DDL. Seules les instructions T-SQL `select` sont prises en charge. Pour plus d’informations sur les principales différences en ce qui concerne T-SQL, consultez [problèmes connus de SQL](./sqlknownissues.md).

## <a name="querying-from-kustoexplorer-with-t-sql"></a>Interrogation de Kusto. Explorer avec T-SQL

L’outil Kusto. Explorer prend en charge les requêtes T-SQL sur Kusto.
Pour ordonner à Kusto. Explorer d’exécuter une requête, commencez la requête par une ligne de commentaire T-SQL vide ( `--` ). Par exemple :

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>À partir de T-SQL vers le langage de requête Kusto

Kusto prend en charge la traduction de requêtes T-SQL en langage de requête Kusto (KQL). Cette traduction peut aider les personnes connaissant SQL à mieux comprendre KQL.
Pour récupérer le KQL équivalent à partir d’une instruction T-SQL `select` , ajoutez `explain` avant la requête.

Par exemple, la requête T-SQL suivante :

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

génère cette sortie :

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```
