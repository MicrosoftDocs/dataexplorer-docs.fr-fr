---
title: Entités - Azure Data Explorer | Microsoft Docs
description: Cet article décrit les entités dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: a69362b590acee99fbe9b57d9303099f0033d458
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128493"
---
# <a name="entity-types"></a>Types d’entités

Les requêtes Kusto s’exécutent dans le contexte d’une base de données Kusto attachée à un cluster Kusto. Les données incluses dans la base de données sont organisées dans des tables, auxquelles la requête peut faire référence, et au sein d’une table, elles sont organisées sous la forme d’une grille rectangulaire de colonnes et de lignes. De plus, les requêtes peuvent faire référence à des fonctions stockées dans la base de données, qui sont des fragments de requête disponibles pouvant être réutilisés.

* Les clusters sont des entités qui contiennent des bases de données.
  Les clusters n’ont pas de nom, mais peuvent être référencés en utilisant la fonction spéciale `cluster()` avec l’URI du cluster.
  Par exemple, `cluster("https://help.kusto.windows.net")` est une référence à un cluster qui contient la base de données `Samples`.

* Les [bases de données](./databases.md) sont des entités nommées qui contiennent des tables et des fonctions stockées. Toutes les requêtes Kusto s’exécutent dans le contexte d’une base de données, et les entités de cette base de données peuvent être référencées par la requête sans qualification. De plus, d’autres bases de données du cluster ou d’autres clusters peuvent être référencées à l’aide de la [fonction spéciale database()](../databasefunction.md). Par exemple : `cluster("https://help.kusto.windows.net").database("Samples")`
  est une référence universelle à une base de données spécifique.

* Les [tables](./tables.md) sont des entités nommées qui contiennent des données. Une table présente un ensemble ordonné de colonnes et zéro ligne de données ou plus, chaque ligne contenant une seule valeur de données pour chacune des colonnes de la table. Les tables peuvent être référencées par nom uniquement si elles se trouvent dans la base de données dans le contexte de la requête, ou en les qualifiant avec une référence de base de données sinon. Par exemple, `cluster("https://help.kusto.windows.net").database("Samples").StormEvents` est une référence universelle à une table particulière dans la base de données `Samples`.
  Les tables peuvent également être référencées à l’aide de la [fonction spéciale table()](../tablefunction.md).

* Les [colonnes](./columns.md) sont des entités nommées qui ont un [type de données scalaire](../scalar-data-types/index.md).
  Les colonnes sont référencées dans la requête par rapport au flux de données tabulaire se trouvant dans le contexte de l’opérateur spécifique qui les référence.

* Les [fonctions stockées](./stored-functions.md) sont des entités nommées qui permettent de réutiliser des requêtes ou des parties de requêtes Kusto.

* Les [tables externes](./externaltables.md) sont des entités qui référencent les données stockées en dehors de la base de données Kusto.
  Les tables externes sont utilisées pour exporter des données de Kusto vers un stockage externe, ainsi que pour interroger des données externes sans les ingérer dans Kusto.