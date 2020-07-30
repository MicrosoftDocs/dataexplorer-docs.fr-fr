---
title: REPEAT ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit REPEAT () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0d488ac96cd3db2161761ea837d5490d25cfc920
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345825"
---
# <a name="repeat"></a>repeat()

Génère un tableau dynamique contenant une série de valeurs égales.

## <a name="syntax"></a>Syntaxe

`repeat(`*valeur* `,` *nombre*`)` 

## <a name="arguments"></a>Arguments

* *valeur*: valeur de l’élément dans le tableau résultant. Le type de *valeur* peut être booléen, entier, long, réel, DateTime ou TimeSpan.   
* *Count*: nombre d’éléments dans le tableau résultant. Le nombre doit être *un nombre entier* .
Si *Count* est égal à zéro, un tableau vide est retourné.
Si *Count* est inférieur à zéro, une valeur null est retournée. 

## <a name="examples"></a>Exemples

L’exemple suivant retourne `[1, 1, 1]`:

```kusto
T | extend r = repeat(1, 3)
```