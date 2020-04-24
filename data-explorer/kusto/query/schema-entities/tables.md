---
title: Tables - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Tables in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: fd47a8f59c716148dfc5f89ac1d4c7aca45a8e9c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509280"
---
# <a name="tables"></a>Tables

Les tables sont des entités nommées qui contiennent des données. Une table dispose d’un ensemble de [colonnes commandées,](./columns.md)et de zéro ou plus de lignes de données, chaque rangée contenant une valeur de données pour chacune des colonnes de la table. L’ordre des lignes dans le tableau est inconnu et n’affecte généralement pas les requêtes, à l’exception de certains opérateurs tabulaires (comme [l’opérateur supérieur)](../topoperator.md)qui sont intrinsèquement indéterminées.

Les tables occupent le même espace de nom que [les fonctions stockées,](./stored-functions.md)donc si une fonction stockée et une table ont toutes deux le même nom, la fonction stockée sera choisie.

**Remarques**  

* Les noms de table sont sensibles aux cas.
* Les noms de table suivent les règles pour les [noms d’entités](./entity-names.md).
* La limite maximale des tableaux par base de données est de 10 000.


Les détails sur la façon de créer et de gérer les tables peuvent être trouvés sous [les tables de gestion](../../management/tables.md)

## <a name="table-references"></a>Références de table

La façon la plus simple de référencer une table est en utilisant son nom. Cela peut être fait pour toutes les tables qui sont dans la base de données dans leur contexte. Ainsi, par exemple, la requête suivante compte les `StormEvents` enregistrements du tableau actuel de la base de données :

```kusto
StormEvents
| count
```

Notez qu’une façon équivalente d’écrire la requête ci-dessus est en échappant au nom de la table:

```kusto
["StormEvents"]
| count
```

Les tableaux peuvent également être référencés en notant explicitement la base de données (ou la base de données et le cluster) dans lesquelles ils se trouvent, ce qui permet d’autoriser des requêtes qui combinent des données provenant de plusieurs bases de données et clusters. Par exemple, la requête suivante fonctionnera avec n’importe quelle base de données dans son contexte, tant que l’appelant aura accès à la base de données cible :

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

Il est également possible de faire référence à un tableau en utilisant la [fonction spéciale de la table,,](../tablefunction.md)tant que l’argument de cette fonction s’évalue à une constante. Par exemple :

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```