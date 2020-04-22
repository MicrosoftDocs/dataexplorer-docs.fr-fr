---
title: .table de drop et .drop tables - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .drop table et .drop tables dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/18/2020
ms.openlocfilehash: 5f3a488aba5a6785ceb6ad4a093c520ec0509e5e
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744757"
---
# <a name="drop-table-and-drop-tables"></a>.table de baisse et .tables de drop

Supprime une table (ou plusieurs tables) de la base de données.

Nécessite [la permission d’administration de table](../management/access-control/role-based-authorization.md).

> [!NOTE]
> La `.drop` `table` commande supprime uniquement les données (c’est-à-dire que les données ne peuvent pas être interrogées, mais elles sont toujours récupérables du stockage persistant). Les artefacts de stockage sous-jacents `recoverability` sont supprimés en fonction de la propriété dans la [politique de conservation](../management/retentionpolicy.md) qui était en vigueur au moment où les données ont été ingérées dans le tableau.

**Syntaxe**

`.drop``table` *TableName* `ifexists`[ ]

`.drop``tables` (*TableName1*, *TableName2*,..) [ifexists]

> [!NOTE]
> Si `ifexists` elle est spécifiée, la commande ne manquera pas s’il y a une table inexistante.

**Exemple**

```kusto
.drop table CustomersTable ifexists
.drop tables (ProductsTable, ContactsTable, PricesTable) ifexists
```

**Retourne**

Cette commande renvoie une liste des tableaux restants dans la base de données. 

| Paramètre de sortie | Type   | Description                             |
|------------------|--------|-----------------------------------------|
| TableName        | String | Nom de la table.                  |
| nom_base_de_données     | String | La base de données à laquelle appartient la table. |