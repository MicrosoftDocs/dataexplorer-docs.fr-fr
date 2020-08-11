---
title: format_ipv4_mask ()-Azure Explorateur de données
description: Cet article décrit format_ipv4_mask () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 08/09/2020
ms.openlocfilehash: ef0b8306ad5d62e7efa9cb037a6fb8d06d415859
ms.sourcegitcommit: ed902a5a781e24e081cd85910ed15cd468a0db1e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88075398"
---
# <a name="format_ipv4_mask"></a>format_ipv4_mask ()

Analyse l’entrée à l’aide d’un masque réseau et retourne une chaîne représentant l’adresse IPv4 en notation CIDR.

```kusto
print format_ipv4_mask('192.168.1.255', 24) == '192.168.1.0/24'
print format_ipv4_mask(3232236031, 24) == '192.168.1.0/24'
```

## <a name="syntax"></a>Syntaxe

`format_ipv4_mask(`*Expr* [ `,` *PrefixMask*`])`

## <a name="arguments"></a>Arguments

* *`Expr`*: Représentation sous forme de chaîne ou de nombre de l’adresse IPv4 en notation CIDR.
* *`PrefixMask`*: (Facultatif) entier de 0 à 32 représentant le nombre de bits les plus significatifs pris en compte. Si argument n’est pas spécifié, tous les masques de bits sont utilisés (32).

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat sera une chaîne représentant l’adresse IPv4 en notation CIDR.
Si la conversion échoue, le résultat sera une chaîne vide.

## <a name="see-also"></a>Voir aussi

- [format_ipv4 ()](format-ipv4-function.md): pour la mise en forme des adresses IPv4 sans notation CIDR.
- [Fonctions IPv4 et IPv6](scalarfunctions.md#ipv4ipv6-functions)

## <a name="examples"></a>Exemples

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(address:string, mask:long)
[
 '192.168.1.1', 24,          
 '192.168.1.1', 32,          
 '192.168.1.1/24', 32,       
 '192.168.1.1/24', long(-1), 
]
| extend result = format_ipv4(address, mask), 
         result_mask = format_ipv4_mask(address, mask)
```

|address|masque|result|result_mask|
|---|---|---|---|
|192.168.1.1|24|192.168.1.0|192.168.1.0/24|
|192.168.1.1|32|192.168.1.1|192.168.1.1/32|
|192.168.1.1/24|32|192.168.1.0|192.168.1.0/24|
|192.168.1.1/24|-1|||
