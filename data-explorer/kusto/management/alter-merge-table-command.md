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
ms.openlocfilehash: 1a58d44e7884fb198f04a9f12a71c77cf164331b
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321690"
---
# <a name="alter-merge-table"></a>.alter-merge table
 
`.alter-merge table`Commande :

* Sécurise les données dans les colonnes existantes
* Ajoute de nouvelles colonnes, `docstring` , et un dossier, à une table existante
* Doit s’exécuter dans le contexte d’une base de données spécifique qui exporte le nom de la table
* Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md)

> [!WARNING]
> L’utilisation `.alter-merge` incorrecte de la commande peut entraîner une perte de données.

> [!TIP]
> `.alter-merge`A un équivalent, la `.alter` commande de table qui a des fonctionnalités similaires. Pour plus d’informations, consultez [`.alter table`](../management/alter-table-command.md)

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
 
