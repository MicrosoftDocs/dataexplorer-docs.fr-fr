---
title: . afficher le schéma de la table-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit. afficher le schéma de la table dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: e2a550f0ea755181d39524876833cff4281608b4
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618341"
---
# <a name="show-table-schema"></a>.show table schema

Obtient le schéma à utiliser dans les commandes CREATE/ALTER et les métadonnées de table supplémentaires.

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

```kusto
.show table TableName cslschema 
```

| Paramètre de sortie | Type   | Description                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | Nom de la table.                                    |
| schéma           | String | Le schéma de table doit être utilisé pour la création/modification de table |
| nom_base_de_données     | String | Base de données à laquelle la table appartient                   |
| Dossier           | String | Dossier de la table                                            |
| DocString        | String | Docstring de la table                                         |


## <a name="show-table-schema-as-json"></a>. afficher le schéma de la table au format JSON

Obtient le schéma au format JSON et les métadonnées de table supplémentaires.

Nécessite l' [autorisation de l’utilisateur de base de données](../management/access-control/role-based-authorization.md).

```kusto
.show table TableName schema as JSON
```

| Paramètre de sortie | Type   | Description                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Le nom de la table                   |
| schéma           | String | Schéma de table au format JSON         |
| nom_base_de_données     | String | Base de données à laquelle la table appartient |
| Dossier           | String | Dossier de la table                          |
| DocString        | String | Docstring de la table                       |
