---
title: Stratégie RowOrder-Azure Explorateur de données
description: Cet article décrit la stratégie RowOrder dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bf8844a72d2b4fc3d9dec4f24fdfa6ac36ee84d7
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967500"
---
# <a name="roworder-policy-command"></a>commande de stratégie rowOrder

Cet article décrit les commandes de contrôle utilisées pour la création et la modification de la [stratégie d’ordre des lignes](../management/roworderpolicy.md).

## <a name="show-roworder-policy"></a>Afficher la stratégie RowOrder

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>Supprimer la stratégie RowOrder

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>Modifier la stratégie RowOrder

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**Exemples** 

L’exemple suivant définit la stratégie d’ordre des lignes sur la `TenantId` colonne (ordre croissant) comme clé primaire et sur la `Timestamp` colonne (croissant) comme clé secondaire. La stratégie est ensuite interrogée.

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|RowOrderPolicy| 
|---|---|
|événements|(TenantId ASC, horodateur DESC)|
