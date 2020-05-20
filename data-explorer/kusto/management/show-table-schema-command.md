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
ms.openlocfilehash: 899e54b46dd231db0bf1272c0eb1933dad474a47
ms.sourcegitcommit: 2ebd83369f247cf6dd91709f26e4ecd873489eaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83555013"
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
.show table TableName schema as json
```

| Paramètre de sortie | Type   | Description                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Le nom de la table                   |
| schéma           | String | Schéma de table au format JSON         |
| nom_base_de_données     | String | Base de données à laquelle la table appartient |
| Dossier           | String | Dossier de la table                          |
| DocString        | String | Docstring de la table                       |
