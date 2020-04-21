---
title: DIFFÉRENCES MS-TDS/T-SQL entre Kusto Microsoft SQL Server - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit LES différences MS-TDS/T-SQL entre Kusto Microsoft SQL Server dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 09/04/2019
ms.openlocfilehash: be294053fdd0f95d488f52b547ef7abd0ef7c23c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523424"
---
# <a name="ms-tdst-sql-differences-between-kusto-microsoft-sql-server"></a>MS-TDS/T-SQL Différences entre Kusto Microsoft SQL Server

Voici la liste partielle des principales différences entre la mise en œuvre de T-SQL par Kusto et SQL Server.

## <a name="create-insert-drop-alter-statements"></a>CREATE, INSÉRER, DROP, MODIFIER LES DÉCLARATIONS

Kusto ne prend pas en charge les modifications de schémas ou de modifications de données par MS-TDS, ni les déclarations ci-dessus T-SQL.

## <a name="correlated-sub-queries"></a>Sous-demandes corrélées

Kusto ne prend pas en charge les `SELECT` `WHERE`sous-requêtes corrélées dans , , et `JOIN` les clauses.

## <a name="top-flavors"></a>Saveurs TOP

Kusto ignore `WITH TIES` et évalue la `TOP`requête comme régulière .
Kusto ne prend `PERCENT`pas en charge .

## <a name="cursors"></a>Curseurs

Kusto ne prend pas en charge les curseurs SQL.

## <a name="flow-control"></a>Contrôle de flux

Kusto ne prend pas en charge les déclarations de `IF` `THEN` `ELSE` contrôle des flux, à `THEN` `ELSE` l’exception de quelques cas limités, comme la clause qui a le schéma identique pour le et les lots.

## <a name="data-types"></a>Types de données

Selon la requête, les données retournées peuvent avoir un type différent de celui de SQL Server.
Un exemple évident ici `TINYINT` sont `SMALLINT` des types tels que et qui n’ont pas d’équivalent dans Kusto. Par conséquent, les clients `BYTE` qui `INT16` s’attendent à une valeur de type ou pourraient obtenir une `INT32` ou une `INT64` valeur à la place.

## <a name="column-order-in-results"></a>Ordre de colonne dans les résultats

Lorsque l’astérix `SELECT` est utilisé dans la déclaration, l’ordre des colonnes dans chaque ensemble de résultat peut différer entre Kusto et SQL Server. Le client qui utilise des noms de colonnes fonctionnerait mieux dans ces cas.
S’il n’y a `SELECT` pas de caractère astérix dans la déclaration, lesordinaux de colonne seraient préservés.

## <a name="columns-name-in-results"></a>Nom des colonnes dans les résultats

Dans T-SQL, plusieurs colonnes peuvent avoir le même nom. Ce n’est pas permis à Kusto.
En cas de collision entre les noms, les noms des colonnes peuvent être différents à Kusto.
Cependant, le nom d’origine serait conservé, au moins pour l’une des colonnes.

## <a name="any-all-and-exists-predicates"></a>N’importe qui, TOUS et EXISTS est prédicé sur

Kusto ne supporte pas `ANY`les `ALL`prédicats , , et `EXISTS`.

## <a name="recursive-ctes"></a>CTE récursives

Kusto ne prend pas en charge les expressions de table communes récursives.

## <a name="dynamic-sql"></a>SQL dynamique

Kusto ne prend pas en charge les déclarations SQL dynamiques (exécution en ligne du script SQL généré par la requête).

## <a name="within-group"></a>WITHIN GROUP

Kusto ne prend `WITHIN GROUP` pas en charge la clause.

## <a name="truncate-function"></a>Fonction TRUNCATE

La fonction TRUNCATE (ODBC) dans Kusto fonctionne de la même façon que ROUND, ce qui signifie que le résultat sera la valeur la plus proche au lieu de la plus basse retournée dans SQL.