---
title: parse_ipv4_mask() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit parse_ipv4_mask () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 994c1e551d7c38bb1493579bcf10438acd2923f2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511779"
---
# <a name="parse_ipv4_mask"></a>parse_ipv4_mask()

Convertit la chaîne d’entrée d’IPv4 et de netmask en représentation à long nombre (signée 64 bits).

```kusto
parse_ipv4_mask("127.0.0.1", 24) == 2130706432

parse_ipv4_mask('192.1.168.2', 31) == parse_ipv4_mask('192.1.168.3', 31) 
```

**Syntaxe**

`parse_ipv4_mask(`*Expr*`, `*PrefixMask*`)`

**Arguments**

* *Expr*: Une représentation en chaîne de l’adresse IPv4 qui sera convertie en long. 
* *PrefixMask*: Un intégré de 0 à 32 représentant le nombre de bits les plus significatifs qui sont pris en compte.

**Retourne**

Si la conversion est réussie, le résultat sera un nombre long.
Si la conversion n’est `null`pas réussie, le résultat sera .