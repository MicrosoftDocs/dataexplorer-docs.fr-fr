---
title: . Alter table-Azure Explorateur de données
description: Cet article décrit la commande. Alter table.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/08/2020
ms.openlocfilehash: 29f0c65a635b6e4fe6ffe3288cc1dcdde702fc8a
ms.sourcegitcommit: be1bbd62040ef83c08e800215443ffee21cb4219
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84665025"
---
# <a name="alter-table"></a>.alter table
 
`.alter table`Commande :
* Sécurise les données dans les colonnes « conservées »
* Réorganiser les colonnes de la table
* Définit un nouveau schéma de colonne, `docstring` , et un nouveau dossier sur une table existante, en remplaçant le schéma de colonne existant, `docstring` et le dossier
* Doit s’exécuter dans le contexte d’une base de données spécifique qui exporte le nom de la table
* Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md)

> [!WARNING]
> L’utilisation `.alter` incorrecte de la commande peut entraîner une perte de données.

> [!TIP]
> `.alter`A un équivalent, la `.alter-merge` commande de table qui a des fonctionnalités similaires. Pour plus d’informations, consultez [. Alter-Merge table](../management/alter-merge-table-command.md)

**Syntaxe**

`.alter``table` *TableName* (*ColumnName*:*ColumnType*,...)  [ `with` `(` [ `docstring` `=` *Documentation*] [ `,` `folder` `=` *NomDossier*] `)` ]


 * La table aura exactement les mêmes colonnes, dans le même ordre que celui spécifié.
 Spécifiez les colonnes de la table :
 * Si des colonnes existantes ne sont pas spécifiées dans la commande, elles sont supprimées et les données qu’elles contiennent sont perdues, comme avec la `.drop column` commande.
 * Lorsque vous modifiez une table, la modification d’un type de colonne n’est pas prise en charge. Utilisez à la place la commande [. ALTER COLUMN](alter-column.md) .

> [!TIP]
> Utilisez `.show table [TableName] cslschema` pour récupérer le schéma de colonne existant avant de le modifier.


Comment la commande affectera-t-elle les données ?
* Les données existantes ne sont pas modifiées physiquement par la commande. Les données dans les colonnes supprimées sont ignorées. Les données dans les nouvelles colonnes sont supposées être null.
* Selon la configuration du cluster, l’ingestion des données peut modifier le schéma de colonne de la table, même sans intervention de l’utilisateur. Lorsque vous apportez des modifications au schéma de colonne d’une table, assurez-vous que l’ingestion n’ajoutera pas les colonnes nécessaires à la suppression de la commande.

> [!WARNING]
> Les processus d’ingestion de données dans la table qui modifient le schéma de colonne de la table, et qui se produisent en parallèle avec la `.alter table` commande, peuvent être exécutés indépendamment de l’ordre des colonnes de la table. Il y a également un risque que les données soient ingérées dans les mauvaises colonnes. Empêchez ces problèmes en arrêtant l’ingestion pendant la commande ou en vous assurant que ces opérations d’ingestion utilisent toujours un objet de mappage.

**Exemples**

```kusto
.alter table MyTable (ColumnX:string, ColumnY:int) 
.alter table MyTable (ColumnX:string, ColumnY:int) with (docstring = "Some documentation", folder = "Folder1")
```
