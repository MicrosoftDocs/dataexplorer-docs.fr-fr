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
ms.openlocfilehash: 8ba9eb06e07dd513d20cdade87322d9ef5f17d24
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321639"
---
# <a name="alter-table-folder"></a>.alter table folder

Modifie la valeur de dossier d’une table existante. 

`.alter``table` *TableName* `folder` *Dossier* TableName

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * L' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la table à l’origine est également autorisé à le modifier
> * Si la table n’existe pas, une erreur est retournée. Pour créer une nouvelle table, consultez [`.create table`](create-table-command.md)

**Exemples** 

```kusto
.alter table MyTable folder "Updated folder"
```

```kusto
.alter table MyTable folder @"First Level\Second Level"
```