---
title: opérateur de tri-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur de tri dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4f9878b77ad2288395a54d5315864e460ca37875
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351027"
---
# <a name="sort-operator"></a>opérateur sort 

Trie les lignes de la table d’entrée dans l’ordre d’après une ou plusieurs colonnes.

```kusto
T | sort by strlen(country) asc, price desc
```

**Alias**

`order`

## <a name="syntax"></a>Syntaxe

*T* `| sort by` *expression* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ] [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: entrée de table à trier.
* *expression*: expression scalaire selon laquelle effectuer le tri. Les valeurs doivent être de type numérique, date, heure ou chaîne.
* `asc` Tri par ordre croissant, de faible à élevé. La valeur par défaut est `desc`, par ordre décroissant allant d’élevé à faible.
* `nulls first`(la valeur par défaut pour `asc` Order) place les valeurs NULL au début et `nulls last` (la valeur par défaut pour `desc` Order) place les valeurs NULL à la fin.

## <a name="example"></a>Exemple

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

Toutes les lignes de la table Traces ayant un `ActivityId`spécifique, triées d’après leur horodatage. Si la `Timestamp` colonne contient des valeurs NULL, celles-ci apparaissent sur les premières lignes du résultat.

Pour exclure les valeurs NULL du résultat, ajoutez un filtre avant l’appel à sort :

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```