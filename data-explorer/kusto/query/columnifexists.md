---
title: column_ifexists() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit column_ifexists() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: c4f7d4a2114aa2b9b3bb8ae3e951306a3ffb725e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517168"
---
# <a name="column_ifexists"></a>column_ifexists()

Prend un nom de colonne comme une chaîne et une valeur par défaut. Renvoie une référence à la colonne si elle existe, sinon - retourne la valeur par défaut.

**Syntaxe**

`column_ifexists(`*colonneName*`, `*defaultValue*)

**Arguments**

* *colonneName*: Le nom de la colonne
* *defaultValue*: La valeur à utiliser si la colonne n’existe pas dans le contexte dans lequel la fonction a été utilisée.
                  Cette valeur peut être n’importe quelle expression scalaire (par exemple une référence à une autre colonne).

**Retourne**

Si *la colonneName* existe, alors la colonne à laquelle elle fait référence. Sinon - *defaultValue*.

**Exemples**

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```