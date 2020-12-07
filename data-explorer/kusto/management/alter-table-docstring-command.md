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
ms.openlocfilehash: fc449cb6a376eba66457855173c6e992e6ca1dbc
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321656"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Modifie la valeur DocString d’une table existante.

`.alter``table` *TableName* `docstring` *Documentation* TableName

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * L' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la table à l’origine est autorisé à le modifier
> * Si la table n’existe pas, une erreur est retournée. Pour créer une table, consultez. [`.create table`](create-table-command.md)

**Exemple** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```
 
