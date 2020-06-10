---
title: . Alter-table de fusion-Azure Explorateur de données
description: Cet article décrit la commande. Alter-Merge table.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: cc4002d9af8b18841714ac9f91809fb18274782f
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84670512"
---
# <a name="alter-merge-table"></a>. Alter-fusionner la table
 
`.alter-merge table`Commande :

* Sécurise les données dans les colonnes existantes
* Ajoute de nouvelles colonnes, `docstring` , et un dossier, à une table existante
* Doit s’exécuter dans le contexte d’une base de données spécifique qui exporte le nom de la table
* Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md)

> [!WARNING]
> L’utilisation `.alter-merge` incorrecte de la commande peut entraîner une perte de données.

> [!TIP]
> `.alter-merge`A un équivalent, la `.alter` commande de table qui a des fonctionnalités similaires. Pour plus d’informations, consultez [. Alter table](../management/alter-table-command.md)

**Syntaxe**

`.alter-merge``table` *TableName* (*ColumnName*:*ColumnType*,...)  [ `with` `(` [ `docstring` `=` *Documentation*] [ `,` `folder` `=` *NomDossier*] `)` ]

Spécifiez les colonnes de la table :
 * Les colonnes qui n’existent pas et que vous spécifiez sont ajoutées à la fin du schéma existant.
 * Si le schéma passé ne contient pas certaines colonnes de table, les colonnes ne seront pas supprimées.
 * Si vous spécifiez une colonne existante avec un type différent, la commande échoue.

> [!TIP]
> Utilisez `.show table [TableName] cslschema` pour récupérer le schéma de colonne existant avant de le modifier.

Comment la commande affectera-t-elle les données ?
* Les données existantes ne sont pas modifiées physiquement par la commande. Les données dans les colonnes supprimées sont ignorées. Les données dans les nouvelles colonnes sont supposées être null.
* Selon la configuration du cluster, l’ingestion des données peut modifier le schéma de colonne de la table, même sans intervention de l’utilisateur. Lorsque vous apportez des modifications au schéma de colonne d’une table, assurez-vous que l’ingestion n’ajoutera pas les colonnes nécessaires à la suppression de la commande.

**Exemples**

```kusto
.alter-merge table MyTable (ColumnX:string, ColumnY:int) 
.alter-merge table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
 
