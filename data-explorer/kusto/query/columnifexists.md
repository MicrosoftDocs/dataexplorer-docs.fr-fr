---
title: column_ifexists ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit column_ifexists () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 570a832673d94d57d69dfa9c8442d56802ecd7ac
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247868"
---
# <a name="column_ifexists"></a>column_ifexists()

Prend un nom de colonne en tant que chaîne et une valeur par défaut. Retourne une référence à la colonne, le cas échéant ; sinon, retourne la valeur par défaut.

## <a name="syntax"></a>Syntaxe

`column_ifexists(`*ColumnName* `, ` *DefaultValue*)

## <a name="arguments"></a>Arguments

* *ColumnName*: nom de la colonne
* *DefaultValue*: valeur à utiliser si la colonne n’existe pas dans le contexte dans lequel la fonction a été utilisée.
                  Cette valeur peut être n’importe quelle expression scalaire (par exemple, une référence à une autre colonne).

## <a name="returns"></a>Retours

Si *ColumnName* existe, il s’agit de la colonne à laquelle il fait référence. Sinon, *DefaultValue*.

## <a name="examples"></a>Exemples

```kusto
.create function with (docstring = "Wraps a table query that allows querying the table even if columnName doesn't exist ", folder="My Functions")
ColumnOrDefault(tableName:string, columnName:string)
{
    // There's no column "Capital" in "StormEvents", therefore, the State column will be used instead
    table(tableName) | project column_ifexists(columnName, State)
}


ColumnOrDefault("StormEvents", "Captial");
```