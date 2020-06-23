---
title: Types de données scalaires - Azure Data Explorer | Microsoft Docs
description: Cet article décrit les types de données scalaires dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 95bb28c81ec3221569758ead8a289bdf81d32d3d
ms.sourcegitcommit: 4f576c1b89513a9e16641800abd80a02faa0da1c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2020
ms.locfileid: "85128646"
---
# <a name="scalar-data-types"></a>Types de données scalaires

Chaque valeur de données (comme la valeur d’une expression ou le paramètre d’une fonction) a un **type de données**. Un type de données est un **type de données scalaire** (l’un des types prédéfinis ci-dessous) ou un **enregistrement défini par l’utilisateur** (une séquence ordonnée de paires nom/type de données scalaire, comme le type de données d’une ligne d’une table).

Kusto fournit un ensemble de types de données système qui définissent tous les types de données utilisables avec Kusto.

> [!NOTE]
> Les types de données définis par l’utilisateur ne sont pas pris en charge dans Kusto.

Le tableau suivant liste les types de données pris en charge par Kusto, ainsi que les alias supplémentaires que vous pouvez utiliser pour y faire référence et un type .NET Framework à peu près équivalent.

| Type       | Nom(s) supplémentaire(s)   | Type .NET équivalent              | gettype()   |
| ---------- | -------------------- | --------------------------------- | ----------- |
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |
| `dynamic`  |                      | `System.Object`                   | `array` ou `dictionary`, ou l’une des autres valeurs |
| `guid`     | `uuid`, `uniqueid`   | `System.Guid`                     | `guid`      |
| `int`      |                      | `System.Int32`                    | `int`       |
| `long`     |                      | `System.Int64`                    | `long`      |
| `real`     | `double`             | `System.Double`                   | `real`      |
| `string`   |                      | `System.String`                   | `string`    |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   |

Tous les types de données non-chaînes incluent une valeur « Null » spéciale, qui représente l’absence de données ou une incompatibilité de données. Par exemple, cette valeur est générée quand vous essayez d’ingérer la chaîne `"abc"` dans une colonne `int`.
Il est impossible de matérialiser cette valeur explicitement, mais vous pouvez détecter si une expression correspond à cette valeur à l’aide de la fonction `isnull()`.

> [!WARNING]
> La prise en charge du type `guid` est incomplète.
> Nous recommandons vivement aux équipes d’utiliser des valeurs de type `string` à la place.
