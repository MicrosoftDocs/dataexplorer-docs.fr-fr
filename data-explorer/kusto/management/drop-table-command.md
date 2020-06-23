---
title: . Drop table et. Drop tables-Azure Explorateur de données
description: Cet article décrit les tables. Drop table et. Drop tables dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 3e1eb57741302d34664f6cd8f256612a6e70bdd1
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85264485"
---
# <a name="drop-table-and-drop-tables"></a>. Drop table et Drop tables

Supprime une table ou plusieurs tables de la base de données.

Nécessite une [autorisation d’administrateur de table](../management/access-control/role-based-authorization.md).

> [!NOTE]
> La `.drop` `table` commande supprime uniquement les données. Autrement dit, les données ne peuvent pas être interrogées, mais elles sont toujours récupérables à partir d’un stockage persistant. Les artefacts de stockage sous-jacents sont supprimés de façon irréversible en fonction de la `recoverability` propriété de la [stratégie de rétention](../management/retentionpolicy.md) qui était en vigueur au moment où les données ont été ingérées dans la table.

**Syntaxe**

`.drop``table` *TableName* [ `ifexists` ]

`.drop``tables`(*TableName1*, *TableName2*,..) [IfExists]

> [!NOTE]
> Si `ifexists` est spécifié, la commande n’échoue pas s’il existe une table inexistante.

**Exemple**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**Retourne**

Cette commande renvoie la liste des tables restantes dans la base de données.

| Paramètre de sortie | Type   | Description                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Nom de la table.                  |
| nom_base_de_données     | String | Base de données à laquelle la table appartient. |
