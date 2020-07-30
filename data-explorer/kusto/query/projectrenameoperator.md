---
title: Project-renommer l’opérateur-Azure Explorateur de données
description: Cet article décrit l’opérateur renommer Project dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 9bc000ffa57d906c3e65e54e9daac5431f8dc276
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346012"
---
# <a name="project-rename-operator"></a>project-rename, opérateur

Renomme les colonnes dans la sortie de résultat.

```kusto
T | project-rename new_column_name = column_name
```

## <a name="syntax"></a>Syntaxe

*T* `| project-rename` *NewColumnName*  =  *ExistingColumnName* [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: table d’entrée.
* *NewColumnName :* Nouveau nom d’une colonne. 
* *ExistingColumnName :* Nom existant d’une colonne. 

## <a name="returns"></a>Retourne

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
