---
title: Tables-Azure Explorateur de données
description: Cet article décrit les tables dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/30/2019
ms.openlocfilehash: fa97777da8173034098037f1aceec385a4c206de
ms.sourcegitcommit: 3848b8db4c3a16bda91c4a5b7b8b2e1088458a3a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/16/2020
ms.locfileid: "84818599"
---
# <a name="tables"></a>Tables

Les tables sont des entités nommées qui contiennent des données. Une table possède un ensemble ordonné de [colonnes](./columns.md)et zéro, une ou plusieurs lignes de données. Chaque ligne contient une valeur de données pour chacune des colonnes de la table. L’ordre des lignes dans la table est inconnu et n’affecte pas les requêtes de manière générale, à l’exception de certains opérateurs tabulaires (tels que l' [opérateur Top](../topoperator.md)) qui sont intrinsèquement indéterminés.

Les tables occupent le même espace de noms que les [fonctions stockées](./stored-functions.md).
Si une fonction stockée et une table ont toutes les deux le même nom, la fonction stockée est sélectionnée.

**Notes**  

* Les noms de table respectent la casse.
* Les noms de tables suivent les règles pour les [noms d’entité](./entity-names.md).
* La limite maximale de tables par base de données est 10 000.


Vous trouverez plus d’informations sur la création et la gestion des tables sous [gestion des tables](../../management/tables.md) .

## <a name="table-references"></a>Références de table

Le moyen le plus simple de référencer une table consiste à utiliser son nom. Cette référence peut être effectuée pour toutes les tables qui se trouvent dans la base de données en contexte. Par exemple, la requête suivante compte les enregistrements de la table de la base de données active `StormEvents` :

```kusto
StormEvents
| count
```

Une méthode équivalente pour écrire la requête ci-dessus consiste à échapper le nom de la table :

```kusto
["StormEvents"]
| count
```

Les tables peuvent également être référencées par la mention explicite de la base de données (ou de la base de données et du cluster) dans laquelle elles se trouvent. Vous pouvez ensuite créer des requêtes qui combinent des données provenant de plusieurs bases de données et clusters. Par exemple, la requête suivante fonctionne avec n’importe quelle base de données en contexte, à condition que l’appelant ait accès à la base de données cible :

```kusto
cluster("https://help.kusto.windows.net").database("Samples").StormEvents
| count
```

Il est également possible de référencer une table à l’aide de la [fonction spéciale table ()](../tablefunction.md), à condition que l’argument de cette fonction corresponde à une constante. Par exemple :

```kusto
let counter=(TableName:string) { table(TableName) | count };
counter("StormEvents")
```

> [!NOTE]
> Utilisez la `table()` fonction Special pour spécifier explicitement l’étendue des données de la table. Par exemple, utilisez cette fonction pour limiter le traitement aux données de la table qui se trouve dans le cache à chaud.
