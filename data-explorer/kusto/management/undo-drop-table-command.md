---
title: . annulation de la suppression de la table-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. annuler la suppression d’une table dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 2682afaa9e589a8787b7e42a83e3bb68a24a2781
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616675"
---
# <a name="undo-drop-table"></a>.undo drop table

`.undo` `drop` La `table` commande rétablit une opération de suppression de table sur une version de base de données spécifique.

**Syntaxe**

`.undo``drop` `as` *NewTableName* *TableName* `version=v` *DB_MajorVersion.DB_MinorVersion* TableName [NewTableName] DB_MajorVersion. DB_MinorVersion `table`

La commande doit être exécutée avec le contexte de base de données.

**Retourne**

Cette commande :
* Retourne la liste d’étendues de table d’origine
* Spécifie pour chaque extension le nombre d’enregistrements que contient l’étendue
* Retourne si l’opération de récupération a réussi ou a échoué
* Retourne la raison de l’échec, le cas échéant.

| ExtentId                             | NumberOfRecords | Statut                   | FailureReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | Couvrir                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1 000            | Couvrir                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | Impossible de récupérer l’étendue | Conteneur d’étendue : 4b47fd84-c7db-4cfb-9378-67c1de7bf154 introuvable, l’extension a été supprimée du stockage et ne peut pas être restaurée |

**Exemples**

```kusto
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```kusto
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**Recherche de la version de base de données requise**

Vous pouvez trouver la version de la base de données avant l’exécution de l' `.show` `journal` opération Drop à l’aide de la commande :

```kusto
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| OriginalEntityVersion |
|-----------------------|
| v 24.3                 |

**Limitations**

Si une commande de vidage a été exécutée sur cette base de données, la commande annuler la suppression de la table ne peut pas être exécutée vers une version antérieure à l’exécution de vidage.

L’extension peut être récupérée uniquement si la période de suppression dure du conteneur d’étendues dans lequel elle réside n’a pas encore été atteinte.

La commande nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md).
