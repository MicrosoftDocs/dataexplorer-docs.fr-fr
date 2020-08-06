---
title: opérateur Top-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur Top dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 52e66205b5ba048e4ec2d160d447082b1bf65de1
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87802908"
---
# <a name="top-operator"></a>opérateur top

Retourne les *N* premiers enregistrements triés selon les colonnes spécifiées.

```kusto
T | top 5 by Name desc nulls last
```

## <a name="syntax"></a>Syntaxe

*Expression T* `| top` *numberOfRows* `by` *Expression* [ `asc`  |  `desc` ] [ `nulls first`  |  `nulls last` ]

## <a name="arguments"></a>Arguments

* *NumberOfRows*: nombre de lignes de *T* à retourner. Vous pouvez spécifier n’importe quelle expression numérique.
* *Expression*: expression scalaire selon laquelle effectuer le tri. Les valeurs doivent être de type numérique, date, heure ou chaîne.
* `asc` ou `desc` (valeur par défaut) peut s’afficher pour indiquer si la sélection provient du bas ou du haut de la plage.
* `nulls first`(valeur par défaut pour `asc` Order) ou `nulls last` (la valeur par défaut pour `desc` Order) peut sembler contrôler si les valeurs NULL seront au début ou à la fin de la plage.

> [!TIP]
> `top 5 by name`est équivalent à l’expression `sort by name | take 5` à la fois des perspectives sémantiques et des performances.

## <a name="see-also"></a>Voir aussi 

* Utilisez l’opérateur [Top imbriqué](topnestedoperator.md) pour produire des résultats hiérarchiques (imbriqués) hiérarchiques.
