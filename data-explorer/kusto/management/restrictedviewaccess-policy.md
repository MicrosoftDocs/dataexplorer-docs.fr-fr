---
title: Gestion de la stratégie Kusto RestrictedViewAccess-Azure Explorateur de données
description: En savoir plus sur les commandes de stratégie RestrictedViewAccess dans Azure Explorateur de données. Consultez Comment afficher, activer, désactiver, modifier et supprimer cette stratégie.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9ec328e3a15af3cedb833354f7e8ecea0fdc22c8
ms.sourcegitcommit: 3d9b4c3c0a2d44834ce4de3c2ae8eb5aa929c40f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002919"
---
# <a name="restricted_view_access-policy-command"></a>Commande de stratégie restricted_view_access

La stratégie *RestrictedViewAccess* est documentée [ici](../management/restrictedviewaccesspolicy.md).

Les commandes de contrôle permettant d’activer ou de désactiver la stratégie sur les tables de la base de données sont les suivantes :

Pour activer/désactiver la stratégie :
```kusto
.alter table TableName policy restricted_view_access true|false
```

Pour activer/désactiver la stratégie de plusieurs tables :
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

Pour afficher la stratégie :
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

Pour supprimer la stratégie (ce qui équivaut à désactiver) :
```kusto
.delete table TableName policy restricted_view_access  
```
