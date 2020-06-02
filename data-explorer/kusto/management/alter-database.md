---
title: . ALTER DATABASE prettyname-Azure Explorateur de données
description: Cet article décrit la `.alter` commande Database Pretty Name.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 0fc445a7d85f52d672b92163cc358d8f3a741c68
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294506"
---
# <a name="alter-database-prettyname"></a>. ALTER DATABASE prettyname

Modifie le nom convivial d’une base de données.

Requiert l' [autorisation DatabaseAdmin](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.alter``database` *DatabaseName* `prettyname` Databasename `'` *DatabasePrettyName*`'`

**Sortie de retour**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données |String |Nom de la base de données.
|PrettyName |String |Nom convivial de la base de données
