---
title: opérateur de facettes - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de facettes dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0b6c87fc044e0f00f77e28e85d89757a69835164
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515315"
---
# <a name="facet-operator"></a>facet, opérateur

Retourne un ensemble de tables, une pour chaque colonne spécifiée.
Chaque tableau spécifie la liste des valeurs prises par sa colonne.
Un tableau supplémentaire peut être `with` créé en utilisant la clause.

**Syntaxe**

*T* `| facet by` *ColumnName* [`, ` ...] [`with (` *filterPipe*`)`

**Arguments**

* *Nom de colonne:* Le nom de la colonne dans l’entrée, à résumer comme un tableau de sortie.
* *filterPipe:* Une expression de requête appliquée à la table d’entrée pour produire l’un des extrants.

**Retourne**

Tables multiples : `with` une pour la clause et une pour chaque colonne.

**Exemple**

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```