---
title: Order, opérateur-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur Order dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 35fa0c29bf00a8696d4e9d08d73c30bba6171ea1
ms.sourcegitcommit: de81b57b6c09b6b7442665e5c2932710231f0773
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87264713"
---
# <a name="order-operator"></a>order, opérateur 

Trie les lignes de la table d’entrée dans l’ordre d’après une ou plusieurs colonnes.

```kusto
T | order by country asc, price desc
```

> [!NOTE]
> L’opérateur Order est un alias de l’opérateur sort. Pour plus d’informations, consultez [opérateur de tri](sortoperator.md)

**Syntaxe**

*T* `| order by` *colonne* [ `asc`  |  `desc` ] [] [ `nulls first`  |  `nulls last` `,` ...]

**Arguments**

* *T*: entrée de table à trier.
* *Column*: colonne de *T* selon laquelle effectuer le tri. Les valeurs doivent être de type numérique, date, heure ou chaîne.
* `asc` Tri par ordre croissant, de faible à élevé. La valeur par défaut est `desc`, par ordre décroissant allant d’élevé à faible.
* `nulls first`(la valeur par défaut pour `asc` Order) place les valeurs NULL au début et `nulls last` (la valeur par défaut pour `desc` Order) place les valeurs NULL à la fin.

