---
title: opérateur de tri - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur de tri dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 638783b28cddc51d64a80096d7d4d6e0f669d354
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81507478"
---
# <a name="sort-operator"></a>opérateur sort 

Trie les lignes de la table d’entrée dans l’ordre d’après une ou plusieurs colonnes.

```kusto
T | sort by strlen(country) asc, price desc
```

**Alias**

`order`

**Syntaxe**

*T* `| sort by` *expression* expression`asc` | `desc`[`nulls first` | `nulls last`]`,` [ ] [ ...]

**Arguments**

* *T*: L’entrée de table à trier.
* *expression*: Une expression scalaire par laquelle trier. Les valeurs doivent être de type numérique, date, heure ou chaîne.
* `asc` Tri par ordre croissant, de faible à élevé. La valeur par défaut est `desc`, par ordre décroissant allant d’élevé à faible.
* `nulls first`(le défaut `asc` de commande) placera les `nulls last` valeurs nulles `desc` au début et (le défaut de commande) placera les valeurs nulles à la fin.

**Exemple**

```kusto
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc nulls first
```

Toutes les lignes de la table Traces ayant un `ActivityId`spécifique, triées d’après leur horodatage. Si `Timestamp` la colonne contient des valeurs nulles, celles-ci apparaîtront aux premières lignes du résultat.

Afin d’exclure les valeurs nulles du résultat ajouter un filtre avant l’appel à trier:

```kusto
Traces
| where ActivityId == "479671d99b7b" and isnotnull(Timestamp)
| sort by Timestamp asc
```