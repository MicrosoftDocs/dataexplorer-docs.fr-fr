---
title: . Alter table et. Alter-Merge table-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit les tables. Alter table et. Alter-Merge dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/17/2020
ms.openlocfilehash: 18b0502754e95ca56d6a7f6946b9330bd42b174a
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616437"
---
# <a name="alter-table-and-alter-merge-table"></a>. Alter table et. Alter-Merge table

La `.alter table` commande définit un nouveau schéma de colonne, DocString et dossier sur une table existante, remplaçant le schéma de colonne, le docstring et le dossier existants. Les données des colonnes existantes qui sont « conservées » par la commande sont conservées (par conséquent, cette commande peut être utilisée, par exemple, pour réorganiser les colonnes d’une table).

La `.alter-merge table` commande ajoute de nouvelles colonnes, DocString et Folder, à une table existante.
Les données des colonnes existantes sont conservées.

Les deux commandes doivent s’exécuter dans le contexte d’une base de données spécifique qui exporte le nom de la table.

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.alter``table` *TableName* (*ColumnName*:*ColumnType*,...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` Documentation] [ `folder` NomDossier] `)`] `=`

`.alter-merge``table` *TableName* (*ColumnName*:*ColumnType*,...)  [`with` `(`[`docstring` `=` *FolderName* *Documentation*`,` Documentation] [ `folder` NomDossier] `)`] `=`

Spécifiez les colonnes que la table doit avoir après l’achèvement de l’opération. 

> [!WARNING]
> L’utilisation `.alter` incorrecte de la commande peut entraîner une perte de données.
> Lisez attentivement les différences entre `.alter` et `.alter-merge` .

`.alter-merge`:

 * Les colonnes qui n’existent pas et que vous spécifiez sont ajoutées à la fin du schéma existant.
 * Si le schéma passé ne contient pas de colonnes de table, il ne sera pas supprimé.
 * Si vous avez spécifié une colonne existante avec un type différent, la commande échoue.

`.alter`uniquement (pas `.alter-merge`) :

 * La table aura exactement les mêmes colonnes, dans le même ordre que celui spécifié.
 * Les colonnes existantes qui ne sont pas spécifiées dans la commande sont supprimées `.drop column`(comme dans) et les données qu’elles contiennent sont perdues.
 * La modification d’un type de colonne n’est pas prise en charge lors de la modification d’une table. Utilisez à la place la commande [. ALTER COLUMN](alter-column.md) .

> [!TIP] 
> Utilisez `.show table [TableName] cslschema` pour récupérer le schéma de colonne existant avant de le modifier. 

Les conditions suivantes s’appliquent aux deux commandes :

1. Les données existantes ne sont pas physiquement modifiées par ces commandes. Les données dans les colonnes supprimées sont ignorées. Les données dans les nouvelles colonnes sont supposées être null.
1. Selon la configuration du cluster, l’ingestion des données peut modifier le schéma de colonne de la table, même sans intervention de l’utilisateur. Par conséquent, lorsque vous apportez des modifications au schéma de colonne d’une table, assurez-vous que l’ingestion n’ajoutera pas les colonnes nécessaires à la suppression de la commande.

> [!WARNING]
> Les processus d’ingestion de données dans la table qui modifient le schéma de colonne de la table, `.alter table` qui se produisent en parallèle avec la commande, peuvent être exécutés indépendamment de l’ordre des colonnes de la table. Il y a également un risque que les données soient ingérées dans les mauvaises colonnes. Pour éviter cela, arrêtez l’ingestion pendant ces commandes ou assurez-vous que ces opérations d’ingestion utilisent toujours un objet de mappage.

**Exemples**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```