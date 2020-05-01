---
title: . ALTER COLUMN-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la colonne. Alter dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: ecf0fa09438f8df5792d8826150d58f06360cace
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617865"
---
# <a name="alter-column"></a>.alter column

Modifie le type de données d’une colonne de table existante.

> [!WARNING]
> Lorsque vous modifiez le type de données d’une colonne, toutes les données préexistantes de cette colonne qui ne sont pas du nouveau type de données sont ignorées dans les requêtes ultérieures et sont remplacées par une [valeur null](../query/scalar-data-types/null-values.md). Après l' `alter column`utilisation de, ces données ne peuvent pas être récupérées, même en utilisant une autre commande pour repasser le type de colonne à une valeur précédente.
> Vous trouverez [ci-dessous](#changing-column-type-without-data-loss) la procédure recommandée pour modifier le type d’une colonne sans perdre de données.

**Syntaxe** 

`.alter``column` [*DatabaseName* `.`] *TableName* `.` *ColumnName* ColumnName `type` *ColumnNewType* ColumnNewType `=`
 
**Exemple** 

```kusto
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>Modification du type de colonne sans perte de données

Pour modifier le type de colonne tout en conservant les données d’historique, créez une nouvelle table correctement typée.

Pour chaque table `T1` pour laquelle vous souhaitez modifier un type de colonne, exécutez les étapes suivantes :

1. Créez une table `T1_prime` avec le schéma correct (les types de colonne de droite).
1. Permutez les tables à l’aide de la commande [. Rename tables](rename-table-command.md) , qui permet de permuter les noms de tables.

```kusto
.rename tables T_prime=T1, T1=T_prime
```

Lorsque la commande est terminée, les nouvelles données sont envoyées `T1` à qui est maintenant tapée correctement et les données d’historique `T1_prime`sont disponibles dans.

Tant `T1_prime` que les données ne sont pas déplacées dans `T1` la fenêtre de rétention, les `T1_prime`requêtes qui touchent doivent être modifiées pour pouvoir effectuer une Union.