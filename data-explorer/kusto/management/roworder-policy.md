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
ms.openlocfilehash: 63aad71854c73a3d1f1837c3665a152db8b48d13
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258026"
---
# <a name="roworder-policy"></a>Stratégie RowOrder

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
