---
title: Opérateur extend – Azure Data Explorer | Microsoft Docs
description: Cet article décrit l’opérateur extend dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: 7ead6313128b99357dd61f18f55c5d5543943a05
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95513350"
---
# <a name="extend-operator"></a>opérateur extend

Crée des colonnes calculées et les ajoute au jeu de résultats.

```kusto
T | extend duration = endTime - startTime
```

## <a name="syntax"></a>Syntaxe

*T* `| extend` [*ColumnName* | `(`*ColumnName*[`,` ...]`)` `=`] *Expression* [`,` ...]

## <a name="arguments"></a>Arguments

* *T*: Jeu de résultats tabulaire d’entrée.
* *ColumnName:* facultatif. Nom de la colonne à ajouter ou mettre à jour. En cas d’omission, le nom est généré. Si l’*Expression* retourne plusieurs colonnes, une liste de noms de colonnes peut être spécifiée entre parenthèses. Dans ce cas, les colonnes de sortie de l’*expression* reçoivent les noms spécifiés, et les éventuelles colonnes de sortie restantes sont supprimées. Si une liste de noms de colonnes n’est pas spécifiée, toutes les colonnes de sortie de l’*Expression* avec des noms générés sont ajoutées à la sortie.
* *Expression :* calcul sur les colonnes de l’entrée.

## <a name="returns"></a>Retours

Copie du jeu de résultats tabulaire d’entrée, par exemple :
1. Les noms de colonnes indiqués par `extend` qui existent déjà dans l’entrée sont supprimés et ajoutés en tant que nouvelles valeurs calculées.
2. Les noms de colonnes indiqués par `extend` qui n’existent pas dans l’entrée sont ajoutés en tant que nouvelles valeurs calculées.

**Conseils**

* L’opérateur `extend` ajoute une colonne au jeu de résultats d’entrée qui n’a **pas** d’index. Dans la plupart des cas, si la nouvelle colonne est définie comme étant exactement la même qu’une colonne de table existante avec un index, Kusto peut utiliser automatiquement l’index existant. Toutefois, dans certains scénarios complexes, cette propagation n’est pas effectuée. Dans ce cas, si l’objectif est de renommer une colonne, utilisez plutôt l’[opérateur `project-rename`](projectrenameoperator.md).

## <a name="example"></a>Exemple

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

Vous pouvez utiliser la fonction [series_stats](series-statsfunction.md) pour retourner plusieurs colonnes.