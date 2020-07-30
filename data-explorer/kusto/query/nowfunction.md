---
title: Now ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit maintenant () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9beae6edd1361715dfe84f851ca0a9bb69f4299c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346573"
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

## <a name="returns"></a>Retourne

Heure UTC actuelle, en tant que `datetime`.

`now()` + *décalage* 

## <a name="example"></a>Exemple

Détermine l’intervalle depuis l’événement identifié par le prédicat :

```kusto
T | where ... | extend Elapsed=now() - Timestamp
```