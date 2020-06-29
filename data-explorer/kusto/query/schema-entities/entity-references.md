---
title: Références d’entité-Azure Explorateur de données
description: Cet article décrit les références d’entité dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9d652ea8551a21d542ad6afef575616e7387183f
ms.sourcegitcommit: 4eb64e72861d07cedb879e7b61a59eced74517ec
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517884"
---
# <a name="entity-references"></a>Références d’entité

Référencez les entités de schéma Kusto dans une requête à l’aide de leurs noms. Les noms d’entité valides sont les *bases de données*, les *tables*, les *colonnes*et les fonctions stockées. Les *clusters* ne peuvent pas être référencés par leurs noms.
Si le conteneur de l’entité n’est pas ambigu dans le contexte actuel, utilisez le nom de l’entité sans qualification supplémentaire. Par exemple, lors de l’exécution d’une requête sur une base de données appelée `DB` , vous pouvez référencer une table appelée `T` dans cette base de données par son nom, `T` .

Si le conteneur de l’entité n’est pas disponible dans le contexte ou si vous souhaitez référencer une entité à partir d’un conteneur différent du conteneur en contexte, utilisez le **nom qualifié**de l’entité.
Le nom est la concaténation du nom d’entité au du conteneur, et éventuellement de ses conteneurs, et ainsi de suite. De cette façon, une requête exécutée sur une base de données `DB` peut faire référence à une table `T1` dans une autre base `DB1` de données du même cluster, à l’aide de `database("DB1").T1` . Si la requête veut référencer une table à partir d’un autre cluster, elle peut le faire, par exemple, à l’aide de `cluster("https://C2.kusto.windows.net/").database("DB2").T2` .

Les références d’entité peuvent également utiliser le nom convivial de l’entité, à condition qu’elle soit unique dans le contexte du conteneur de l’entité. Pour plus d’informations, consultez noms de l' [entité](./entity-names.md#entity-pretty-names).

## <a name="wildcard-matching-for-entity-names"></a>Correspondance des caractères génériques pour les noms d’entité

Dans certains contextes, vous pouvez utiliser un caractère générique ( `*` ) pour faire correspondre tout ou partie d’un nom d’entité. Par exemple, la requête suivante fait référence à toutes les tables de la base de données active, et toutes les tables de la base de données `DB` dont le nom commence par `T` :

```kusto
union *, database("DB1").T*
```

> [!NOTE]
> La correspondance générique ne peut pas correspondre à des noms d’entité qui commencent par un signe dollar ( `$` ).
Ces noms sont réservés au système.

## <a name="next-steps"></a>Étapes suivantes

* [types d’entités de schéma](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/)
* [noms d’entités de schéma](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)
