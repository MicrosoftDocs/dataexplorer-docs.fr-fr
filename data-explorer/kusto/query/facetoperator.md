---
title: opérateur facette-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur facette dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0e5bc062b99a97b8d11c11312aac2d5829d6584b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348018"
---
# <a name="facet-operator"></a>facet, opérateur

Retourne un ensemble de tables, une pour chaque colonne spécifiée.
Chaque table spécifie la liste des valeurs prises par sa colonne.
Une table supplémentaire peut être créée à l’aide de la `with` clause.

## <a name="syntax"></a>Syntaxe

*T* `| facet by` *ColumnName* [ `, ` ...] [ `with (` *filterPipe*`)`

## <a name="arguments"></a>Arguments

* *ColumnName :* Nom de la colonne dans l’entrée, à synthétiser en tant que table de sortie.
* *filterPipe :* Expression de requête appliquée à la table d’entrée pour produire une des sorties.

## <a name="returns"></a>Retourne

Plusieurs tables : une pour la `with` clause, et une pour chaque colonne.

## <a name="example"></a>Exemple

```kusto
MyTable 
| facet by city, eventType 
    with (where timestamp > ago(7d) | take 1000)
```