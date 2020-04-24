---
title: Le type de données réel - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le type de données réel dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: f69b74670fefcff6f6c24d5235f58beb98b0405a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81509671"
---
# <a name="the-real-data-type"></a>Le type de données réel

Le `real` type de données représente un numéro flottant de 64 bits de large, à double précision.

Les littérals du `real` type de données ont la même représentation que . NET `System.Double`. `1.0`, `0.1`, `1e5` et sont tous `real`les littérals de type .

Il existe plusieurs formes littérales spéciales :
* `real(null)`: C’est la [valeur nulle](null-values.md).
* `real(nan)`: Pas un nombre (NaN). Par exemple, le résultat `0.0` de `0.0`la division d’un par un autre .
* `real(+inf)`: Infini positif. Par exemple, le `1.0` résultat `0.0`de la division par .
* `real(-inf)`: Infini négatif. Par exemple, le `-1.0` résultat `0.0`de la division par .