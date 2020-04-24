---
title: opérateur d’impression - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur d’impression dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/16/2019
ms.openlocfilehash: b751b07446a74ef17002abac26e4c881a2da757b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510963"
---
# <a name="print-operator"></a>print, opérateur

Sorties à une seule rangée avec une ou plusieurs expressions scalaires.

```kusto
print x=1, s=strcat("Hello", ", ", "World!")
```

**Syntaxe**

`print`[*ColumnName* `=`] *ScalarExpression* [','...]

**Arguments**

* *ColumnName*: Un nom d’option à attribuer à la colonne singulière de la sortie.
* *ScalarExpression*: Une expression scalaire à évaluer.

**Retourne**

Une table à colonne unique, à une seule rangée, dont la seule cellule a la valeur de la *ScalarExpression*évaluée.

**Exemples**

L’opérateur `print` est utile comme un moyen rapide d’évaluer une ou plusieurs expressions scalaires et de faire une table à une rangée des valeurs résultantes.
Par exemple :

```kusto
print 0 + 1 + 2 + 3 + 4 + 5, x = "Wow!"
```

```kusto
print banner=strcat("Hello", ", ", "World!")
```