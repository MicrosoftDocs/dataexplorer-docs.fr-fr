---
title: .alter dossier de table - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le dossier de table .alter dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/06/2020
ms.openlocfilehash: a1d368d50f0e42acdbc25348b8025fbe8b530536
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522302"
---
# <a name="alter-table-folder"></a>.alter table folder

Modifie la valeur De dossier d’une table existante. 

`.alter``table` *Dossier TableName* `folder` *Folder*

> [!NOTE]
> * Nécessite [l’autorisation d’administration de base de données](../management/access-control/role-based-authorization.md)
> * [L’utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la table à l’origine est également autorisé à la modifier
> * Si le tableau n’existe pas, une erreur est retournée. Pour créer une nouvelle table, voir [.créer table](create-table-command.md)

**Exemples** 

```
.alter table MyTable folder "Updated folder"
```

```
.alter table MyTable folder @"First Level\Second Level"
```