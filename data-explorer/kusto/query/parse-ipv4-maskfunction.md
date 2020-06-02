---
title: parse_ipv4_mask ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la fonction parse_ipv4_mask () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: fd6bd97befc376d13573a0a85169524cf01b9d2d
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294608"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

Convertit la chaîne d’entrée de IPv4 et du masque réseau en une représentation de nombre long (signée 64 bits).

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432
parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31)
```

**Syntaxe**

`parse_ipv4_mask(`*`Expr`*`, `*`PrefixMask`*`)`

**Arguments**

* *`Expr`*: Représentation sous forme de chaîne de l’adresse IPv4 qui sera convertie en longue. 
* *`PrefixMask`*: Entier compris entre 0 et 32 représentant le nombre de bits les plus significatifs pris en compte.

**Retourne**

Si la conversion réussit, le résultat sera un nombre long.
Si la conversion échoue, le résultat sera `null` .
