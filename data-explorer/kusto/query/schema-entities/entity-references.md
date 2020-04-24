---
title: Références d’entités - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les références d’entité dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abbf63de632ff2ff5fb721dddc256c5ee62d4966
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509399"
---
# <a name="entity-references"></a>Références d’entité

Les entités de schémas Kusto (bases de données, tableaux, colonnes et fonctions stockées, mais pas les clusters) sont référencées dans la requête en utilisant leurs noms. Si le conteneur de l’entité est sans ambiguïté dans le contexte actuel, le nom de l’entité peut être utilisé sans qualifications supplémentaires. Par exemple, lors de l’exécution `DB`d’une requête `T` contre une base de `T`données appelée , on peut faire référence à un tableau appelé dans cette base de données simplement en utilisant son nom, .

Si, d’autre part, le conteneur de l’entité n’est pas disponible dans le contexte, ou si l’on veut référencer une entité à partir d’un conteneur différent du conteneur dans son contexte, il faut utiliser le **nom qualifié**de l’entité, qui est la concatenation du nom de l’entité à celui du conteneur (et potentiellement de son conteneur, etc.) Ainsi, une requête en `DB` cours d’exécution contre la base de données peut se référer à un `T1` tableau dans une base de données `DB1` différente d’un même cluster en utilisant `database("DB1").T1`, et si elle veut référencer une table à partir d’un autre cluster, il peut le faire, par exemple, en utilisant `cluster("https://C2.kusto.windows.net/").database("DB2").T2`.

Les références d’entité peuvent également le joli nom de l’entité, tant qu’elle est unique dans le contexte du conteneur de l’entité. Voir [entité joli noms](./entity-names.md#entity-pretty-names).

## <a name="wildcard-matching-for-entity-names"></a>Wildcard correspondant pour les noms d’entités

Dans certains contextes, on peut`*`utiliser une wildcard ( ) pour correspondre à tout ou partie d’un nom d’entité. Par exemple, la requête suivante fait référence à toutes les tables `DB` de la `T`base de données actuelle, ainsi qu’à toutes les tables de base de données dont le nom commence par :

```kusto
union *, database("DB1").T*
```

Remarque : L’appariement Wildcard ne correspond`$`pas aux noms d’entités qui commencent par un signe en dollars ().
Ces noms sont réservés au système.



