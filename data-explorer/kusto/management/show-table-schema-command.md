---
title: .show table schéma - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .show schéma de table dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/04/2020
ms.openlocfilehash: 9223baa0242cc936b25a7d3293d102cb3966ed53
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81519769"
---
# <a name="show-table-schema"></a>.show schéma de table

Obtient le schéma à utiliser dans les commandes de création / modification et des métadonnées de table supplémentaires.

Nécessite la [permission de l’utilisateur de la base de données](../management/access-control/role-based-authorization.md).

```
.show table TableName cslschema 
```
| Paramètre de sortie | Type   | Description                                               |
|------------------|--------|-----------------------------------------------------------|
| TableName        | String | Nom de la table.                                    |
| schéma           | String | Le schéma de table comme il faut l’utiliser pour créer/modifier la table |
| nom_base_de_données     | String | La base de données à laquelle appartient la table                   |
| Dossier           | String | Dossier de table                                            |
| DocString (en)        | String | La table est docstring                                         |


## <a name="show-table-schema-as-json"></a>.show schéma de table comme JSON

Obtient le schéma en format JSON et des métadonnées de table supplémentaires.

Nécessite la [permission de l’utilisateur de la base de données](../management/access-control/role-based-authorization.md).

```
.show table TableName schema as JSON
```

| Paramètre de sortie | Type   | Description                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Le nom de la table                   |
| schéma           | String | Le schéma de table en format JSON         |
| nom_base_de_données     | String | La base de données à laquelle appartient la table |
| Dossier           | String | Dossier de table                          |
| DocString (en)        | String | La table est docstring                       |