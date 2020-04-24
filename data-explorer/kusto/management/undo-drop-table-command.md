---
title: .undo drop table - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .undo drop table dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 55fd08ce2624c522b971e1ee3862f7d11c6f679f
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519548"
---
# <a name="undo-drop-table"></a>.undo drop table

`.undo` `drop` La `table` commande retourne une opération de table de chute à une version spécifique de base de données.

**Syntaxe**

`.undo``drop` `version=v` *DB_MajorVersion.DB_MinorVersion* *TableName* `as` *NewTableName*TableName [ NewTableName ] DB_MajorVersion.DB_MinorVersion `table`

La commande doit être exécutée dans le contexte de la base de données.

**Retourne**

Cette commande :
* Retourne la liste d’étendues de table d’origine
* Spécifie pour chaque étendue le nombre d’enregistrements que l’étendue contient
* Retours si l’opération de récupération a réussi ou échoué
* Retourne la raison de l’échec, si cela est pertinent.

| ExtentId (extentId)                             | NombreOfRecords | Statut                   | ÉchecReason                                                                                                                  |
|--------------------------------------|-----------------|--------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ef296c9e-d75d-44bc-985c-b93dd2519691 | 100             | Récupéré                |
| 370b30d7-cf2a-4997-986e-3d05f49c9689 | 1 000            | Récupéré                |
| 861f18a5-6cde-4f1e-a003-a43506f9e8da | 855             | Incapable de récupérer l’étendue | Conteneur d’étendue : 4b47fd84-c7db-4cfb-9378-67c1de7bf154 n’a pas été trouvé, l’étendue a été retirée du stockage et ne peut pas être restaurée |

**Exemples**

```
// Recover TestTable table to database version 24.3
.undo drop table TestTable version="v24.3"
```

```
// Recover TestTable table to database version 10.3 with new table name, NewTestTable (can be used if a table with the same name was already created since the drop)  
.undo drop table TestTable as NewTestTable version="v10.3"
```

**Comment trouver la version de base de données requise**

Vous pouvez trouver la version de base de `.show` `journal` données avant l’opération de chute a été exécutée en utilisant la commande :

```
.show database TestDB journal | where Event == "DROP-TABLE" and EntityName == "TestTable" | project OriginalEntityVersion 
```

| OriginalEntityVersion |
|-----------------------|
| v24.3                 |

**Limitations**

Si une commande de purge a été exécutée sur cette base de données, la commande de table de dépôt annuler ne peut pas être exécutée à une version plus tôt à l’exécution de purge.

L’étendue ne peut être récupérée que si la période de suppression dure du conteneur d’étendue dans lequel il réside n’a pas encore été atteinte.

La commande nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md).