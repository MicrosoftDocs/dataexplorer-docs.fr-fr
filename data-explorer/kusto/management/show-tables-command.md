---
title: . afficher les tables-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. afficher les tables dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 3da4f705d3182c52d06c7767a12d9be15a219e5c
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618324"
---
# <a name="show-tables"></a>. afficher les tables

Retourne un jeu qui contient la table spécifiée ou toutes les tables de la base de données.

Nécessite l' [autorisation de la visionneuse de base de données](../management/access-control/role-based-authorization.md).

```kusto
.show tables
.show tables (T1, ..., Tn)
```

**Sortie**

|Paramètre de sortie |Type |Description
|---|---|---
|TableName  |String |Nom de la table.
|nom_base_de_données  |String |Base de données à laquelle la table appartient.
|Dossier |String |Dossier de la table.
|DocString |String |Chaîne qui documente la table.

**Exemple de sortie**

|Nom de la table |Nom de la base de données |Dossier | DocString
|---|---|---|---
|Table1 |DB1 |Journaux d’activité |Contient les journaux des services
|Table2 |DB1 | Signalement |
|Table3 |DB1 | | Informations étendues |
|Table4 |DB2 | Mesures| Contient des informations sur les performances des services
