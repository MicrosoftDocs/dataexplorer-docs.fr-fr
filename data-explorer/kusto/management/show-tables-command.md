---
title: .tables d’exposition - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .show tables dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: a8faf307a241d1ba0f73436d9503a56c9078e471
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519633"
---
# <a name="show-tables"></a>.tables d’exposition

Renvoie un ensemble qui contient la table spécifiée ou toutes les tables de la base de données.

Nécessite la [permission du spectateur de base de données](../management/access-control/role-based-authorization.md).

```
.show tables
.show tables (T1, ..., Tn)
```

**Sortie**

|Paramètre de sortie |Type |Description
|---|---|---
|TableName  |String |Nom de la table.
|nom_base_de_données  |String |La base de données à laquelle appartient la table.
|Dossier |String |Le dossier de la table.
|DocString (en) |String |Une chaîne documentant la table.

**Exemple de sortie**

|Nom de la table |Nom de la base de données |Dossier | DocString (en)
|---|---|---|---
|Table1 |DB1 |Journaux d’activité |Contient des journaux de services
|Table2 |DB1 | Signalement |
|Table3 |DB1 | | Informations étendues |
|Tableau4 |DB2 | Mesures| Contient des informations sur le rendement des services