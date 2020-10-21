---
title: Now ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit maintenant () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 63b99b4774c46872b83e526ca00a5d974f0b0c19
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249807"
---
# <a name="now"></a>now()

Retourne l’heure UTC actuelle, décalée éventuellement en fonction d’un intervalle de temps donné.
Cette fonction peut être utilisée plusieurs fois dans une instruction, et l’heure référencée est la même pour toutes les instances.

```kusto
now()
now(-2d)
```

## <a name="syntax"></a>Syntaxe

`now(`[*décalage*]`)`

## <a name="arguments"></a>Arguments

* *offset*: `timespan` , ajouté à l’heure UTC actuelle. Valeur par défaut : 0.

## <a name="returns"></a>Retours

Heure UTC actuelle, en tant que `datetime`.

`now()` + *décalage* 

## <a name="example"></a>Exemple

Détermine l’intervalle depuis l’événement identifié par le prédicat :

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```