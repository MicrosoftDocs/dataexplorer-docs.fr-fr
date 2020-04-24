---
title: .drop base de données joliname - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit .drop base de données prettyname dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a8a74b871ddcf420f39bb45ebdf3bce36f026105
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81521180"
---
# <a name="drop-database-prettyname"></a>.drop base de données joliname

Laisse tomber le joli nom (amical) d’une base de données.
Nécessite [databaseAdmin permission](../management/access-control/role-based-authorization.md).

**Syntaxe**

`.drop` `database` *nom_base_de_données* `prettyname`

**Sortie de retour**
 
|Paramètre de sortie |Type |Description 
|---|---|---
|nom_base_de_données |String |Nom de la base de données.
|PrettyName (En) |String |Le joli nom de base de données (null après l’opération de largage)