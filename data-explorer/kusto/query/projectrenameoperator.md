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
ms.openlocfilehash: 68581cfe4b3828823ced7d4704eb08df5d2aefa7
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83373171"
---
# <a name="project-rename-operator"></a>project-rename, opérateur

Renomme les colonnes dans la sortie de résultat.

```kusto
T | project-rename new_column_name = column_name
```

**Syntaxe**

*T* `| project-rename` *NewColumnName*  =  *ExistingColumnName* [ `,` ...]

**Arguments**

* *T*: table d’entrée.
* *NewColumnName :* Nouveau nom d’une colonne. 
* *ExistingColumnName :* Nom existant d’une colonne. 

**Retourne**

Table qui contient les colonnes dans le même ordre que dans une table existante, avec les colonnes renommées.


**Exemples**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|
