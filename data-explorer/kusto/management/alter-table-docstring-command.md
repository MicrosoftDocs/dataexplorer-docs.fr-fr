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
ms.openlocfilehash: 790cd9805be6dd4440ef2eb51c504044dc069b3c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82617780"
---
# <a name="alter-table-docstring"></a>.alter table docstring

Modifie la valeur DocString d’une table existante.

`.alter``table` *TableName* `docstring` *Documentation* TableName

> [!NOTE]
> * Nécessite une [autorisation d’administrateur de base de données](../management/access-control/role-based-authorization.md)
> * La modification de la table est également autorisée pour l' [utilisateur de base de données](../management/access-control/role-based-authorization.md) qui a créé la table à l’origine
> * Si la table n’existe pas, une erreur est retournée. Pour créer une table, consultez [. Create table](create-table-command.md)

**Exemple** 

```kusto
.alter table LyricsAsTable docstring "This is the theme to Garry's show"
```