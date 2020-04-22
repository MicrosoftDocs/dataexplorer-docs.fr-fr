---
title: Types de données scalaires - Azure Data Explorer | Microsoft Docs
description: Cet article décrit les types de données scalaires dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 01/27/2020
ms.openlocfilehash: 3ef87217beee62fe4cecf7ee95dfe8daba49af7a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81490240"
---
# <a name="scalar-data-types"></a>Types de données scalaires

Chaque valeur de données (comme la valeur d’une expression ou le paramètre d’une fonction) a un **type de données**. Un type de données est un **type de données scalaire** (l’un des types prédéfinis ci-dessous) ou un **enregistrement défini par l’utilisateur** (une séquence ordonnée de paires nom/type de données scalaire, comme le type de données d’une ligne d’une table).

Kusto fournit un ensemble de types de données système qui définissent tous les types de données utilisables avec Kusto.

> [!NOTE]
> Les types de données définis par l’utilisateur ne sont pas pris en charge dans Kusto.

Le tableau suivant liste les types de données pris en charge par Kusto, ainsi que les alias supplémentaires que vous pouvez utiliser pour y faire référence et un type .NET Framework à peu près équivalent.

| Type       | Nom(s) supplémentaire(s)   | Type .NET équivalent              | gettype()   |Type de stockage (nom interne)|
| ---------- | -------------------- | --------------------------------- | ----------- |----------------------------|
| `bool`     | `boolean`            | `System.Boolean`                  | `int8`      |`I8`                        |
| `datetime` | `date`               | `System.DateTime`                 | `datetime`  |`DateTime`                  |
| `dynamic`  |                      | `System.Object`                   | `array` ou `dictionary`, ou l’une des autres valeurs |`Dynamic`|
| `guid`     | `uuid`, `uniqueid`   | `System.Guid`                     | `guid`      |`UniqueId`                  |
| `int`      |                      | `System.Int32`                    | `int`       |`I32`                       |
| `long`     |                      | `System.Int64`                    | `long`      |`I64`                       |
| `real`     | `double`             | `System.Double`                   | `real`      |`R64`                       |
| `string`   |                      | `System.String`                   | `string`    |`StringBuffer`              |
| `timespan` | `time`               | `System.TimeSpan`                 | `timespan`  |`TimeSpan`                  |
| `decimal`  |                      | `System.Data.SqlTypes.SqlDecimal` | `decimal`   | `Decimal`                  |

Tous les types de données incluent une valeur « Null » spéciale, qui représente l’absence de données ou une incompatibilité de données. Par exemple, cette valeur est générée quand vous essayez d’ingérer la chaîne `"abc"` dans une colonne `int`.
Il est impossible de matérialiser cette valeur explicitement, mais vous pouvez détecter si une expression correspond à cette valeur à l’aide de la fonction `isnull()`.

> [!WARNING]
> À la rédaction de cet article, la prise en charge du type `guid` est incomplète. Nous recommandons vivement aux équipes d’utiliser des valeurs de type `string` à la place.

