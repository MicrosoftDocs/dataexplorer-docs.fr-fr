---
title: Requêtes-Azure Explorateur de données
description: Cet article décrit les requêtes dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 8be14a48f5b24d344454d9aa5012f48e7d7e2b8e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250958"
---
# <a name="query-operators"></a>Opérateurs de requête

Une requête est une opération en lecture seule sur les données ingérées d’un cluster Kusto Engine. Les requêtes s’exécutent toujours dans le contexte d’une base de données particulière du cluster. Ils peuvent également faire référence à des données d’une autre base de données, ou même dans un autre cluster.

Étant donné que la requête ad hoc de données est le scénario de priorité supérieure pour Kusto, la syntaxe du langage de requête Kusto est optimisée pour les utilisateurs non experts qui créent et exécutent des requêtes sur leurs données et qui peuvent comprendre clairement ce que fait chaque requête (logiquement).

La syntaxe du langage est celle d’un Data Flow, où « Data » signifie « données tabulaires » (données dans une ou plusieurs formes rectangulaires de lignes/colonnes). Au minimum, une requête se compose de références de données sources (références à des tables Kusto) et d’un ou plusieurs **opérateurs de requête** appliqués dans l’ordre, indiqués visuellement par l’utilisation d’un caractère de barre verticale ( `|` ) pour délimiter les opérateurs.

Par exemple :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents 
| where State == 'FLORIDA' and StartTime > datetime(2000-01-01)
| count
```

Chaque filtre précédé de la barre verticale `|` est une instance d’un *opérateur*, assortie de certains paramètres. L’entrée de l’opérateur est la table résultant du pipeline précédent. Dans la plupart des cas, tous les paramètres sont des [expressions scalaires](./scalar-data-types/index.md) sur les colonnes de l’entrée.
Dans certains cas, les paramètres correspondent aux noms des colonnes d’entrée et, parfois, le paramètre est une seconde table. Le résultat d’une requête est toujours une table, même si elle ne contient qu’une colonne et qu’une ligne.

`T` est utilisé dans la requête pour désigner le pipeline ou la table source précédent.
