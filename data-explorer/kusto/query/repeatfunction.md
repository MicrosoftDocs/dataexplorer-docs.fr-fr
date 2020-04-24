---
title: répéter() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la répétition () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fd944112a64e7400e38c627b7b0651bb6fccd54
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510419"
---
# <a name="repeat"></a>repeat()

Génère un tableau dynamique avec une série de valeurs égales.

**Syntaxe**

`repeat(`*nombre de valeur* `,` *count*`)` 

**Arguments**

* *valeur*: La valeur de l’élément dans le tableau résultant. Le type de *valeur* peut être boolean, integer, long, réel, datetime, ou timepan.   
* *compter*: Le nombre des éléments dans le tableau résultant. Le *compte* doit être un numéro d’intégrant.
Si *le nombre* est égal à zéro, un tableau vide est retourné.
Si *le nombre* est inférieur à zéro, une valeur nulle est retournée. 

**Exemples**

L’exemple suivant retourne `[1, 1, 1]`:

```kusto
T | extend r = repeat(1, 3)
```