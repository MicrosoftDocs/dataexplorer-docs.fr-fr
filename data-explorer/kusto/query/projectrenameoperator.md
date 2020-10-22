---
title: Project-renommer l’opérateur-Azure Explorateur de données
description: Cet article décrit l’opérateur renommer Project dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9c46c8d0271516c56c3549212ad7b74f721ce81b
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356501"
---
# <a name="project-rename-operator"></a>project-rename, opérateur

Renomme les colonnes dans la sortie de résultat.

```kusto
T | project-rename new_column_name = column_name
```

## <a name="syntax"></a>Syntax

*T* `| project-rename` *NewColumnName*  =  *ExistingColumnName* [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: table d’entrée.
* *NewColumnName :* Nouveau nom d’une colonne. 
* *ExistingColumnName :* Nom existant d’une colonne. 

## <a name="returns"></a>Retours

Table qui contient les colonnes dans le même ordre que dans une table existante, avec les colonnes renommées.

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|
