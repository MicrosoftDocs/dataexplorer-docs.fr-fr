---
title: opérateur d’impression-Azure Explorateur de données
description: Cet article décrit l’opérateur d’impression dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: d5788ee937fe110b63a8f137fdab0790eb7cb37e
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373204"
---
# <a name="print-operator"></a>print, opérateur

Génère une seule ligne avec une ou plusieurs expressions scalaires.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**Syntaxe**

`print`[*ColumnName* `=` ] *ScalarExpression* [', '...]

**Arguments**

* *ColumnName*: nom d’option à assigner à la colonne singulière de la sortie.
* *ScalarExpression*: expression scalaire à évaluer.

**Retourne**

Une table à une seule colonne et une seule ligne, dont la cellule unique a la valeur du *ScalarExpression*évalué.

**Exemples**

L' `print` opérateur est utile pour évaluer rapidement une ou plusieurs expressions scalaires et rendre une table à une seule ligne parmi les valeurs résultantes.
Par exemple :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```
<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print banner=strcat("Hello", ", ", "World!")
```
