---
title: parse_ipv4_mask ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la fonction parse_ipv6_mask () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: ec4c398d9079cd5e01875bd1d92ae06db0bb0908
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512569"
---
# <a name="parse_ipv6_mask"></a>parse_ipv6_mask()
 
Convertit la chaîne IPv6/IPv4 et le masque réseau en une représentation sous forme de chaîne IPv6 canonique.

```kusto
parse_ipv6_mask("127.0.0.1", 24) == '0000:0000:0000:0000:0000:ffff:7f00:0000'
parse_ipv6_mask(":fe80::85d:e82c:9446:7994", 120) == 'fe80:0000:0000:0000:085d:e82c:9446:7900'
```

**Syntaxe**

`parse_ipv6_mask(`*`Expr`*`, `*`PrefixMask`*`)`

**Arguments**

* *`Expr`*: Expression de chaîne représentant l’adresse réseau IPv6/IPv4 qui sera convertie en représentation IPv6 canonique. La chaîne peut inclure le masque net à l’aide [de la notation du préfixe IP](#ip-prefix-notation).
* *`PrefixMask`*: Entier compris entre 0 et 128 représentant le nombre de bits les plus significatifs pris en compte.

## <a name="ip-prefix-notation"></a>Notation de préfixe IP

Les adresses IP peuvent être définies à `IP-prefix notation` l’aide d’une barre oblique ( `/` ).
L’adresse IP à gauche de la barre oblique ( `/` ) est l’adresse IP de base. Le nombre (1 à 127) à droite de la barre oblique ( `/` ) est le nombre de 1 bit contigu dans le masque réseau.

**Retourne**

Si la conversion réussit, le résultat sera une chaîne représentant une adresse réseau IPv6 canonique.
Si la conversion échoue, le résultat sera `null` .

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 // IPv4 addresses
 '192.168.255.255',     120,  // 120-bit netmask is used
 '192.168.255.255/24',  124,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255', 128,  // 128-bit netmask is used
 // IPv6 addresses
 'fe80::85d:e82c:9446:7994', 128,     // 128-bit netmask is used
 'fe80::85d:e82c:9446:7994/120', 124, // 120-bit netmask is used
 // IPv6 with IPv4 notation
 '::192.168.255.255',    128,  // 128-bit netmask is used
 '::192.168.255.255/24', 128,  // 120-bit netmask is used, as IPv4 address doesn't use upper 8 bits
]
| extend ip6_canonical = parse_ipv6_mask(ip_string, netmask)
```

|ip_string|masque réseau|ip6_canonical|
|---|---|---|
|192.168.255.255|120|0000:0000:0000:0000:0000 : FFFF : c0a8 : FF00|
|192.168.255.255/24|124|0000:0000:0000:0000:0000 : FFFF : c0a8 : FF00|
|255.255.255.255|128|0000:0000:0000:0000:0000 : FFFF : FFFF : FFFF|
|FE80 :: 85D : E82C : 9446:7994|128|FE80:0000:0000:0000:085D : E82C : 9446:7994|
|FE80 :: 85D : E82C : 9446:7994/120|124|FE80:0000:0000:0000:085D : E82C : 9446:7900|
|:: 192.168.255.255|128|0000:0000:0000:0000:0000 : FFFF : c0a8 : FFFF|
|:: 192.168.255.255/24|128|0000:0000:0000:0000:0000 : FFFF : c0a8 : FF00|

