---
title: Politique RowOrder - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique rowOrder dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: cda4c9a6017071878832fab376a0376d250f3ed6
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520245"
---
# <a name="roworder-policy"></a>Politique RowOrder

Cet article décrit les commandes de contrôle utilisées pour créer et modifier la [politique d’ordre](../management/roworderpolicy.md)des rangées .

## <a name="show-roworder-policy"></a>Afficher la politique RowOrder

```kusto
.show table <table_name> policy roworder

.show table * policy roworder
```

## <a name="delete-roworder-policy"></a>Supprimer la politique RowOrder

```kusto
.delete table <table_name> policy roworder
```

## <a name="alter-roworder-policy"></a>Politique Alter RowOrder

```kusto
.alter table <table_name> policy roworder (<row_order_policy>)

.alter tables (<table_name> [, ...]) policy roworder (<row_order_policy>)

.alter-merge table <table_name> policy roworder (<row_order_policy>)
```

**Exemples**

L’exemple suivant définit la politique `TenantId` d’ordre de ligne pour être sur `Timestamp` la colonne (ascendant) comme clé primaire, et sur la colonne (ascendant) comme clé secondaire ; il interroge ensuite la politique :

```kusto
.alter table events policy roworder (TenantId asc, Timestamp desc)

.alter tables (events1, events2, events3) policy roworder (TenantId asc, Timestamp desc)

.show table events policy roworder 
```

|TableName|RowOrderPolicy RowOrderPolicy RowOrderPolicy RowOrd| 
|---|---|
|événements|(TenantId asc, Timestamp desc)| 