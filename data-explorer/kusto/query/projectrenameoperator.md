---
title: opérateur de renom du projet - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de renommeurs de projet dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1f7fbf2efbfd3ed1dfac9129ec21f5a13c51481c
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81510861"
---
# <a name="project-rename-operator"></a>project-rename, opérateur

Renomme les colonnes dans la sortie du résultat.

```kusto
T | project-rename new_column_name = column_name
```

**Syntaxe**

*T* `| project-rename` *NewColumnName* = *ExistingColumnName* [...]`,`

**Arguments**

* *T*: La table d’entrée.
* *NewColumnName:* Le nouveau nom d’une colonne. 
* *ExistingColumnName:* Le nom existant d’une colonne. 

**Retourne**

Un tableau qui a les colonnes dans le même ordre que dans une table existante, avec des colonnes rebaptisées.


**Exemples**

```kusto
print a='a', b='b', c='c'
|  project-rename new_b=b, new_a=a
```

|new_a|new_b|c|
|---|---|---|
|a|b|c|