---
title: Extend, opérateur-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur Extend dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 32100f6668c2fb20ae715b985b0bf3612e13e69b
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348154"
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

## <a name="returns"></a>Retourne

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