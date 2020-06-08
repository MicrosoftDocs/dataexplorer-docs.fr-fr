---
title: . Alter table DocString-Azure Explorateur de données
description: Cet article décrit la `.alter table docstring` commande dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 65b71ab15763506683c461f04975d22d396f6405
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512535"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Modifie la valeur DocString d’une table existante.

`.alter``table` *TableName* `docstring` *Documentation* TableName

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * L' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la table à l’origine est autorisé à le modifier
> * Si la table n’existe pas, une erreur est retournée. Pour créer une table, consultez [. Create table](create-table-command.md)

**Exemple** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
