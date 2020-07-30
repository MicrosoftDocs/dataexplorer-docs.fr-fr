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
ms.openlocfilehash: 9a5a780a6f7bdf277566d1c0421c5ca2a3a93602
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346063"
---
# <a name="print-operator"></a>print, opérateur

Génère une seule ligne avec une ou plusieurs expressions scalaires.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

## <a name="syntax"></a>Syntaxe

`print`[*ColumnName* `=` ] *ScalarExpression* [', '...]

## <a name="arguments"></a>Arguments

* *ColumnName*: nom d’option à assigner à la colonne singulière de la sortie.
* *ScalarExpression*: expression scalaire à évaluer.

## <a name="returns"></a>Retours

Une table à une seule colonne et une seule ligne, dont la cellule unique a la valeur du *ScalarExpression*évalué.

## <a name="examples"></a>Exemples

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
