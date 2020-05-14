---
title: Kusto MS-TDS/T-SQL différences avec SQL Server-Azure Explorateur de données
description: Cet article décrit les différences MS-TDS/T-SQL entre les Microsoft SQL Server Kusto dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: c949b5bb3659d82ed586a39b4310495e61934777
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382367"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>Différences MS-TDS/T-SQL entre Kusto Microsoft SQL Server

Vous trouverez ci-dessous la liste partielle des principales différences entre les implémentations de Kusto et de SQL Server de T-SQL.

## <a name="create-insert-drop-alter-statements"></a>CRÉER, insérer, supprimer, modifier des instructions

Kusto ne prend pas en charge les modifications de schéma ou les modifications de données via MS-TDS et ne prend pas non plus en charge les instructions T-SQL ci-dessus.

## <a name="correlated-sub-queries"></a>Sous-requêtes corrélées

Kusto ne prend pas en charge les sous-requêtes corrélées dans `SELECT` `WHERE` les `JOIN` clauses, et.

## <a name="top-flavors"></a>PRINCIPALES versions

Kusto ignore `WITH TIES` et évalue la requête comme régulière `TOP` .
Kusto ne prend pas en charge `PERCENT` .

## <a name="cursors"></a>Curseurs

Kusto ne prend pas en charge les curseurs SQL.

## <a name="flow-control"></a>Contrôle de flux

Kusto ne prend pas en charge les instructions de contrôle de Flow, à l’exception de quelques cas limités, tels que `IF` `THEN` `ELSE` la clause qui a le même schéma pour les `THEN` `ELSE` lots et.

## <a name="data-types"></a>Types de données

Selon la requête, les données retournées peuvent avoir un type différent de celui de SQL Server.
Voici un exemple évident de types tels que `TINYINT` et `SMALLINT` qui n’ont pas d’équivalent dans Kusto. Par conséquent, les clients qui attendent une valeur de type `BYTE` ou `INT16` peuvent obtenir un `INT32` ou une valeur à la `INT64` place.

## <a name="column-order-in-results"></a>Ordre des colonnes dans les résultats

Lorsque astérisque est utilisé dans l' `SELECT` instruction, l’ordre des colonnes dans chaque jeu de résultats peut varier entre Kusto et SQL Server. Les clients qui utilisent des noms de colonnes fonctionnent mieux dans ces cas.
S’il n’y a aucun caractère astérisque dans l' `SELECT` instruction, les ordinaux de colonne sont conservés.

## <a name="columns-name-in-results"></a>Nom des colonnes dans les résultats

Dans T-SQL, plusieurs colonnes peuvent avoir le même nom. Cela n’est pas autorisé dans Kusto.
En cas de collision dans les noms, les noms des colonnes peuvent être différents dans Kusto.
Toutefois, le nom d’origine est conservé, au moins pour l’une des colonnes.

## <a name="any-all-and-exists-predicates"></a>Prédicats ANY, ALL et EXISTs

Kusto ne prend pas en charge les prédicats `ANY` , `ALL` et `EXISTS` .

## <a name="recursive-ctes"></a>CTE récursives

Kusto ne prend pas en charge les expressions de table communes récursives.

## <a name="dynamic-sql"></a>SQL dynamique

Kusto ne prend pas en charge les instructions SQL dynamiques (exécution inline du script SQL généré par la requête).

## <a name="within-group"></a>WITHIN GROUP

Kusto ne prend pas en charge la `WITHIN GROUP` clause.

## <a name="truncate-function"></a>Truncate (fonction)

La fonction Truncate (ODBC) dans Kusto fonctionne de la même façon que ROUND, ce qui signifie que le résultat sera la valeur la plus proche au lieu de celle qui est la plus basse renvoyée dans SQL.