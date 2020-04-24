---
title: .alter table et table .alter-fusion - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .alter table et .alter-merge table dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 516c5c7b85f7c310188fd11ae24e52cb23423143
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522370"
---
# <a name="alter-table-and-alter-merge-table"></a>.modifier la table et .alter-merge table

La `.alter table` commande définit un nouveau schéma de colonne, docstring, et dossier à une table existante, l’emporter sur le schéma de colonne existant, docstring, et dossier. Les données dans les colonnes existantes qui sont «conservées» par la commande sont conservées (de sorte que cette commande peut être utilisée, par exemple, pour réorganiser les colonnes d’une table).

La `.alter-merge table` commande ajoute de nouvelles colonnes, docstring et dossier, à une table existante.
Les données des colonnes existantes sont conservées.

Les deux commandes doivent s’exécuter dans le cadre d’une base de données spécifique qui porte le nom de table.

Nécessite [la permission de l’administrateur de table](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.alter``table` *TableName* *(colonneName*:*columnType*, ...)  [`with` `(``docstring` `=` *Documentation*]`,` `folder` `=` [ *FolderName*] `)`]

`.alter-merge``table` *TableName* *(colonneName*:*columnType*, ...)  [`with` `(``docstring` `=` *Documentation*]`,` `folder` `=` [ *FolderName*] `)`]

Spécifier les colonnes que la table devrait avoir après l’achèvement réussi. 

> [!WARNING]
> L’utilisation incorrecte de la `.alter` commande peut entraîner une perte de données.
> Lisez attentivement les différences `.alter` `.alter-merge` entre et ci-dessous.

`.alter-merge`:

 * Des colonnes qui n’existent pas et que vous spécifiez sont ajoutées à la fin du schéma existant.
 * Si le schéma passé ne contient pas certaines colonnes de table, ils ne seront pas supprimés.
 * Si vous avez spécifié une colonne existante avec un type différent, la commande échouera.

`.alter`seulement (pas `.alter-merge`):

 * Le tableau aura exactement les mêmes colonnes, dans le même ordre, comme spécifié.
 * Les colonnes existantes qui ne sont pas `.drop column`spécifiées dans la commande seront supprimées (comme dans ) et les données en elles sont perdues.
 * Modifier un type de colonne n’est pas pris en charge lors de la modification d’une table. Utilisez la commande [de colonne .alter](alter-column.md) à la place.

> [!TIP] 
> Utilisez `.show table [TableName] cslschema` pour obtenir le schéma de colonne existant avant de le modifier. 

Ce qui suit s’applique aux deux commandes :

1. Les données existantes ne sont pas physiquement modifiées par ces commandes. Les données des colonnes supprimées sont ignorées. Les données dans de nouvelles colonnes sont supposées nulles.
1. Selon la configuration du cluster, l’ingestion de données peut modifier le schéma de colonne de la table, même sans interaction utilisateur. Par conséquent, lorsque vous modifiez le schéma de colonne d’une table, assurez-vous que l’ingestion n’ajoutera pas les colonnes nécessaires que la commande supprimera ensuite.

> [!WARNING]
> Les processus d’ingestion de données dans la table qui modifient `.alter table` le schéma de colonne de la table, qui se produisent en parallèle avec la commande, peuvent être exécutés agnostique à l’ordre des colonnes de table. Il y a également un risque que les données soient ingérées dans les mauvaises colonnes. Empêchez cela en arrêtant l’ingestion pendant de telles commandes, ou en s’assurant que ces opérations d’ingestion utilisent toujours un objet de cartographie.

**Exemples**

```
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```