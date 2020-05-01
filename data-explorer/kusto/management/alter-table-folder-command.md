---
title: . Alter table Folder-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit le dossier. Alter table dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: f03a2927d3f0a4fe7ee1719594f2d1f19e231d0f
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618358"
---
# <a name="alter-table-folder"></a>.alter table folder

Modifie la valeur de dossier d’une table existante. 

`.alter``table` *TableName* `folder` *Dossier* TableName

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * L' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la table à l’origine est également autorisé à le modifier
> * Si la table n’existe pas, une erreur est retournée. Pour créer une table, consultez [. Create table](create-table-command.md)

**Exemples** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```