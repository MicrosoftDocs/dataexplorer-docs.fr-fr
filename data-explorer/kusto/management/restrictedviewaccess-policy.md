---
title: Politique RestrictedViewAccess - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la politique de RestrictedViewAccess dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9fcf37d30bfe3ab0f9c4b5d4a720e6a5ba4ffe34
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81520449"
---
# <a name="restrictedviewaccess-policy"></a>Politique RestrictedViewAccess

La politique *RestrictedViewAccess* est documentée [ici](../management/restrictedviewaccesspolicy.md).

Les commandes de contrôle pour activer ou désactiver la stratégie sur la table dans la base de données sont les suivantes :

Pour activer/désactiver la politique :
```kusto
.alter table TableName policy restricted_view_access true|false
```

Pour activer/désactiver la politique de plusieurs tableaux :
```kusto
.alter tables (TableName1, ..., TableNameN) policy restricted_view_access true|false
```

Pour voir la politique :
```kusto
.show table TableName policy restricted_view_access  

.show table * policy restricted_view_access  
```

Supprimer la politique (équivalente à la désactivation) :
```kusto
.delete table TableName policy restricted_view_access  
```