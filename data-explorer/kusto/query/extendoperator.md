---
title: Extend, opérateur-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur Extend dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0398efc3f97e9af1f994b16b91a9888fb4fcfa0b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92243323"
---
# <a name="extend-operator"></a>opérateur extend

Créer des colonnes calculées et les ajouter au jeu de résultats.

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>Syntaxe

*T* `| extend` [*ColumnName*  |  `(` *ColumnName*[ `,` ...] `)` `=` ] *expression* [ `,` ...]

## <a name="arguments"></a>Arguments

* *T*: jeu de résultats tabulaire d’entrée.
* *ColumnName :* Facultatif. Nom de la colonne à ajouter ou mettre à jour. En cas d’omission, le nom est généré. Si l' *expression* retourne plusieurs colonnes, une liste de noms de colonnes peut être spécifiée entre parenthèses. Dans ce cas, les colonnes de sortie de l' *expression*reçoivent les noms spécifiés, en supprimant le reste des colonnes de sortie, le cas échéant. Si une liste de noms de colonnes n’est pas spécifiée, toutes les colonnes de sortie de l' *expression*avec des noms générés sont ajoutées à la sortie.
* *Expression :* Calcul sur les colonnes de l’entrée.

## <a name="returns"></a>Retours

Une copie du jeu de résultats tabulaire d’entrée, de telle sorte que :
1. Les noms de colonne notés par `extend` qui existent déjà dans l’entrée sont supprimés et ajoutés en tant que nouvelles valeurs calculées.
2. Les noms de colonnes marqués par `extend` qui n’existent pas dans l’entrée sont ajoutés en tant que nouvelles valeurs calculées.

**Conseils**

* L' `extend` opérateur ajoute une nouvelle colonne au jeu de résultats d’entrée, qui n’a **pas** d’index. Dans la plupart des cas, si la nouvelle colonne est définie comme étant exactement la même qu’une colonne de table existante avec un index, Kusto peut utiliser automatiquement l’index existant. Toutefois, dans certains scénarios complexes, cette propagation n’est pas effectuée. Dans ce cas, si l’objectif est de renommer une colonne, utilisez plutôt l' [ `project-rename` opérateur](projectrenameoperator.md) .

## <a name="example"></a>Exemple

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

Vous pouvez utiliser la fonction [series_stats](series-statsfunction.md) pour retourner plusieurs colonnes.