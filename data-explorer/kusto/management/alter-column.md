---
title: .alter colonne - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .alter colonne dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/11/2020
ms.openlocfilehash: d41b4f452125fbfebc319112db244deaca79f37a
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522608"
---
# <a name="alter-column"></a>.alter colonne

Modifie le type de données d’une colonne de table existante.

> [!WARNING]
> Lors de la modification du type de données d’une colonne, toutes les données préexistantes dans cette colonne qui n’est pas du nouveau type de données seront ignorées dans les requêtes futures et seront remplacées par une [valeur nulle](../query/scalar-data-types/null-values.md). Après `alter column`avoir utilisé , que les données ne peuvent pas être récupérées, même en utilisant une autre commande pour modifier le type de colonne à une valeur précédente.
> Voir [ci-dessous](#changing-column-type-without-data-loss) pour la procédure recommandée pour changer le type de colonne sans perdre de données.

**Syntaxe** 

`.alter``column` [*DatabaseName* `.`] *TableName* `.` *ColumnName* `type` `=` *ColumnNewType*
 
**Exemple** 

```
.alter column ['Table'].['ColumnX'] type=string
```

## <a name="changing-column-type-without-data-loss"></a>Changer le type de colonne sans perte de données

Pour modifier le type de colonne tout en conservant les données historiques, créez un nouveau tableau correctement tapé.

Pour chaque `T1` table que vous souhaitez modifier un type de colonne, exécutez les étapes suivantes :

1. Créez une `T1_prime` table avec le schéma correct (les bons types de colonnes).
1. Échangez les tables à l’aide de [tables .rename](rename-table-command.md) commande, ce qui permet d’échanger des noms de table.

```
.rename tables T_prime=T1, T1=T_prime
```

Lorsque la commande se termine, `T1` les nouvelles données s’écoulent vers `T1_prime`qui est maintenant tapé correctement et les données historiques sont disponibles en .

Jusqu’à ce que `T1_prime` les données `T1` sortent de la fenêtre de `T1_prime`rétention, les requêtes touchantes doivent être modifiées pour effectuer l’union avec .