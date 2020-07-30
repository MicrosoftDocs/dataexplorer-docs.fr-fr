---
title: bin_auto ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit bin_auto () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 6df5d9793f2d076eb8f97156e911fb49aba4cc9c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349157"
---
# <a name="bin_auto"></a>bin_auto()

Arrondit les valeurs à un « chutier » de taille fixe, avec un contrôle sur la taille de l’emplacement et le point de départ fourni par une propriété de requête.

## <a name="syntax"></a>Syntaxe

`bin_auto` `(` *Expression* `)`

## <a name="arguments"></a>Arguments

* *Expression*: expression scalaire d’un type numérique indiquant la valeur à arrondir.

**Propriétés de la demande du client**

* `query_bin_auto_size`: Un littéral numérique indiquant la taille de chaque emplacement.
* `query_bin_auto_at`: Un littéral numérique indiquant une valeur d' *expression* qui est un « point fixe » (autrement dit, une valeur `fixed_point` pour laquelle `bin_auto(fixed_point)` == `fixed_point` .)

## <a name="returns"></a>Retourne

Le multiple le plus proche de l' `query_bin_auto_at` *expression*ci-dessous, décalé pour `query_bin_auto_at` être traduit en lui-même.

## <a name="examples"></a>Exemples

```kusto
set query_bin_auto_size=1h;
set query_bin_auto_at=datetime(2017-01-01 00:05);
range Timestamp from datetime(2017-01-01 00:05) to datetime(2017-01-01 02:00) step 1m
| summarize count() by bin_auto(Timestamp)
```

|Timestamp                    | count_|
|-----------------------------|-------|
|2017-01-01 00:05:00.0000000  | 60    |
|2017-01-01 01:05:00.0000000  | 56    |