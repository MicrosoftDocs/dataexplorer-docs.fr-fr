---
title: .alter base de données prettyname - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .alter base de données prettyname dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 1b362b547dc980676108ec169a0abb97f189375b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522591"
---
# <a name="alter-database-prettyname"></a>.modifier la base de données joliname

Modifie le joli nom (amical) d’une base de données.

Nécessite [databaseAdmin permission](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.alter``database` *DatabaseName* `prettyname` `'` *DatabasePrettyName*`'`

**Sortie de retour**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données |String |Nom de la base de données.
|PrettyName (En) |String |Le joli nom de la base de données.

