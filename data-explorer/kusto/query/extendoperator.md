---
title: opérateur d’extension - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur d’extension dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4b9b7bdb9488b0e9d1b72b3e0ab4782020c9b841
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515587"
---
# <a name="extend-operator"></a>opérateur extend

Créez des colonnes calculées et appendicez-les à l’ensemble de résultats.

```kusto
T | extend duration = endTime - startTime
```

**Syntaxe**

*T* `| extend` [*ColumnName* | `(`*ColumnName*[`,` ...] ] Expression`,` [...] *Expression* `)` `=`

**Arguments**

* *T*: L’ensemble de résultats tabulaires d’entrée.
* *Nom de colonne:* Optionnel. Le nom de la colonne à ajouter ou à mettre à jour. S’il est omis, le nom sera généré. Si *Expression* renvoie plus d’une colonne, une liste de noms de colonnes peut être spécifiée entre parenthèses. Dans ce cas, les colonnes de sortie *d’Expression*recevront les noms spécifiés, laissant tomber le reste des colonnes de sortie, s’il y en a. Si une liste des noms de colonnes n’est pas spécifiée, toutes les colonnes de sortie *d’Expression*avec des noms générés seront ajoutées à la sortie.
* *Expression:* Un calcul sur les colonnes de l’entrée.

**Retourne**

Une copie de l’ensemble de résultats tabulaires d’entrée, de sorte que :
1. Les noms `extend` de colonnes notés par qui existent déjà dans l’entrée sont supprimés et annexés comme leurs nouvelles valeurs calculées.
2. Les noms `extend` de colonnes notés par qui n’existent pas dans l’entrée sont annexés comme leurs nouvelles valeurs calculées.

**Conseils**

* L’opérateur `extend` ajoute une nouvelle colonne à l’ensemble de résultats d’entrée, qui n’a **pas** d’indice. Dans la plupart des cas, si la nouvelle colonne est définie pour être exactement la même qu’une colonne de table existante qui a un index, Kusto peut automatiquement utiliser l’index existant. Cependant, dans certains scénarios complexes, cette propagation n’est pas faite. Dans de tels cas, si l’objectif est de renommer une colonne, utilisez [ `project-rename` l’opérateur](projectrenameoperator.md) à la place.

**Exemple**

```kusto
Logs
| extend
    Duration = CreatedOn - CompletedOn
    , Age = now() - CreatedOn
    , IsSevere = Level == "Critical" or Level == "Error"
```

Vous pouvez utiliser la fonction [series_stats](series-statsfunction.md) pour retourner plusieurs colonnes.