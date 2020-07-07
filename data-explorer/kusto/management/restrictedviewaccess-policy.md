---
title: Gestion de la stratégie Kusto RestrictedViewAccess-Azure Explorateur de données
description: Cet article décrit la stratégie RestrictedViewAccess dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 33f21bdad11555ad2a55f285cbf40239236c561f
ms.sourcegitcommit: b08b1546122b64fb8e465073c93c78c7943824d9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85967602"
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