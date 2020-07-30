---
title: Next ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la rubrique suivante () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a265d536f655df3086ece1b9953eaade4717781c
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346624"
---
# <a name="next"></a>next()

Retourne la valeur d’une colonne d’une ligne à un offset suivant la ligne actuelle dans un jeu de [lignes sérialisé](./windowsfunctions.md#serialized-row-set).

## <a name="syntax"></a>Syntaxe

`next(column)`

`next(column, offset)`

`next(column, offset, default_value)`

## <a name="arguments"></a>Arguments

* `column`: colonne à partir de laquelle les valeurs sont extraites.

* `offset`: décalage à faire avancer dans les lignes. Si aucun décalage n’est spécifié, un décalage par défaut de 1 est utilisé.

* `default_value`: valeur par défaut à utiliser lorsqu’il n’existe aucune ligne suivante à partir de laquelle prendre la valeur. Si aucune valeur par défaut n’est spécifiée, la valeur null est utilisée.


## <a name="examples"></a>Exemples
```kusto
Table | serialize | extend nextA = next(A,1)
| extend diff = A - nextA
| where diff > 1

Table | serialize nextA = next(A,1,10)
| extend diff = A - nextA
| where diff <= 10
```