---
title: . Alter table DocString-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. Alter table DocString dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 5b3fa21b8b9012fe23a82afea77b1a3e24ae3c91
ms.sourcegitcommit: e1e35431374f2e8b515bbe2a50cd916462741f49
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82108471"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Modifie la valeur DocString d’une table existante.

`.alter``table` *TableName* `docstring` *Documentation* TableName

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * La modification de la table est également autorisée pour l' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la table à l’origine
> * Si la table n’existe pas, une erreur est retournée. Pour créer une table, consultez [. Create table](create-table-command.md)

**Exemple** 

```
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```