---
title: T-SQL - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit T-SQL dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d262d8b7587296c02a2a31d850919af0931bcde6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523407"
---
# <a name="t-sql"></a>T-SQL

Le service Kusto peut interpréter et exécuter des requêtes T-SQL avec quelques limitations linguistiques.
Bien que le [langage de requête Kusto](../../query/index.md) soit la langue préférée pour Kusto, un tel soutien est utile pour l’outil existant qui ne peut pas être facilement converti pour utiliser la langue de requête préférée, et pour l’utilisation occasionnelle de Kusto par des personnes familières avec SQL.

> [!NOTE]
> Kusto ne prend pas en charge une commande DDL `SELECT` de cette manière, seules les déclarations de T-SQL sont prises en charge. Voir [SQL questions connues](./sqlknownissues.md) pour plus de détails sur les principales différences entre SQL Server et Kusto en ce qui concerne T-SQL.

## <a name="querying-kusto-from-kustoexplorer-with-t-sql"></a>Requête Kusto de Kusto.Explorer avec T-SQL

L’outil Kusto.Explorer prend en charge l’envoi de requêtes T-SQL à Kusto.
Pour demander à Kusto.Explorer d’exécuter une requête dans ce mode, précipiez la requête d’une ligne de commentaires T-SQL vide. Par exemple :

```sql
--
select * from StormEvents
```

## <a name="from-t-sql-to-kusto-query-language"></a>De T-SQL à kusto langage de requête

Kusto soutient la traduction des requêtes T-SQL au langage de requête Kusto. Cela peut être utilisé, par exemple, par des personnes familières avec SQL qui veulent mieux comprendre la langue de requête Kusto. Pour récupérer le langage de requête Kusto équivalent `SELECT` à une `EXPLAIN` déclaration T-SQL, il suffit d’ajouter avant la requête.

Par exemple, la requête suivante de T-SQL :

```sql
--
explain
select top(10) *
from StormEvents
order by DamageProperty desc
```

Produit cette sortie:

```kusto
StormEvents
| sort by DamageProperty desc nulls first
| take 10
```