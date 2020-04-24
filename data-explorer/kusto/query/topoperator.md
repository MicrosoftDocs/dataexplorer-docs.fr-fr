---
title: opérateur supérieur - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le meilleur opérateur d’Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bb1d24dd0cd1dfeecb1925e474eb77e8cb86121f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505914"
---
# <a name="top-operator"></a>opérateur top

Retourne les *N* premiers enregistrements triés d’après les colonnes spécifiées.

```kusto
T | top 5 by Name desc nulls last
```

**Syntaxe**

*T* `| top` *NumberOfRows* `by` `asc` | `desc``nulls first` | `nulls last` *Expression* [ ] ]

**Arguments**

* *NumberOfRows*: Le nombre de rangées de *T* à revenir. Vous pouvez spécifier n’importe quelle expression numérique.
* *Expression*: Une expression scalaire par laquelle trier. Les valeurs doivent être de type numérique, date, heure ou chaîne.
* `asc` ou `desc` (valeur par défaut) peut s’afficher pour indiquer si la sélection provient du bas ou du haut de la plage.
* `nulls first`(le défaut `asc` de `nulls last` commande) ou `desc` (le défaut de commande) peut sembler contrôler si les valeurs nulles seront au début ou à la fin de la plage.


**Conseils**

* `top 5 by name`est équivalent à `sort by name | take 5` l’expression à la fois du point de vue sémantique et de performance.
* Utilisez [l’opérateur imbriqué](topnestedoperator.md) haut de gamme pour produire des résultats hiérarchiques (imbriqués).