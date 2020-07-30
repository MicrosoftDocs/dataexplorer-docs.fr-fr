---
title: week_of_year ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit week_of_year () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 82678a68166061fc7b8a30c7cb2e019c8d3d9e0c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87338556"
---
# <a name="week_of_year"></a>week_of_year ()

Retourne un entier qui représente le numéro de la semaine. Le numéro de semaine est calculé à partir de la première semaine de l’année, qui est celle qui comprend le premier jeudi, conformément à la [norme ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Week_dates).

```kusto
week_of_year(datetime("2015-12-14"))
```

## <a name="syntax"></a>Syntaxe

`week_of_year(`*a_date*`)`

## <a name="arguments"></a>Arguments

* `a_date` : une `datetime`.

## <a name="returns"></a>Retourne

`week number`: Numéro de semaine qui contient la date donnée.

## <a name="examples"></a>Exemples

|Entrée                                    |Output|
|-----------------------------------------|------|
|`week_of_year(datetime(2020-12-31))`     |`53`  |
|`week_of_year(datetime(2020-06-15))`     |`25`  |
|`week_of_year(datetime(1970-01-01))`     |`1`   |
|`week_of_year(datetime(2000-01-01))`     |`52`  |

> [!NOTE]
> `weekofyear()`est une variante obsolète de cette fonction. `weekofyear()`n’était pas conforme à la norme ISO 8601 ; la première semaine de l’année a été définie en tant que semaine avec le premier mercredi de l’année.
La version actuelle de cette fonction, `week_of_year()` , est conforme à la norme ISO 8601 ; la première semaine de l’année est définie comme la semaine avec le premier jeudi de l’année.