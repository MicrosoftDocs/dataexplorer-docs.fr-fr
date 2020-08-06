---
title: ipv6_compare ()-Azure Explorateur de données
description: Cet article décrit la fonction ipv6_compare () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: ce14c466d927dd2431ae8e057bceec0d5fdd38b8
ms.sourcegitcommit: 3dfaaa5567f8a5598702d52e4aa787d4249824d4
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87803894"
---
# <a name="ipv6_compare"></a>ipv6_compare()

Compare deux chaînes d’adresse réseau IPv6 ou IPv4. Les deux chaînes IPv6 sont analysées et comparées en tenant compte du masque de préfixe IP combiné calculé à partir des préfixes d’arguments et de l' `PrefixMask` argument facultatif.

```kusto
ipv6_compare('::ffff:7f00:1', '127.0.0.1') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995')  < 0
ipv6_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv6_compare('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == 0
ipv6_compare('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == 0
```

> [!Note]
> La fonction peut accepter et comparer des arguments qui représentent à la fois des adresses réseau IPv6 et IPv4. Toutefois, si l’appelant sait que les arguments sont au format IPv4, utilisez la fonction [ipv4_is_compare ()](./ipv4-comparefunction.md) . Cette fonction permet d’obtenir de meilleures performances d’exécution.

## <a name="syntax"></a>Syntaxe

`ipv6_compare(`*Expr1* `, ` *Expr2* `[ ,` *PrefixMask*`])`

## <a name="arguments"></a>Arguments

* *Expr1*, *expr2*: expression de chaîne représentant une adresse IPv6 ou IPv4. Les chaînes IPv6 et IPv4 peuvent être masquées à l’aide de la notation de préfixe IP (voir remarque).
* *PrefixMask*: entier compris entre 0 et 128 représentant le nombre de bits les plus significatifs pris en compte.

## <a name="ip-prefix-notation"></a>Notation de préfixe IP

Il est courant de définir des adresses IP à `IP-prefix notation` l’aide d’une barre oblique ( `/` ).
L’adresse IP à gauche de la barre oblique ( `/` ) est l’adresse IP de base, et le nombre (de 1 à 127) à droite de la barre oblique ( `/` ) est le nombre de 1 bits contigus dans le masque réseau. 

Par exemple, FE80 :: 85D : E82C : 9446:7994/120 aura un net/Masque_Sous_réseau associé contenant 120 bits contigus.

## <a name="returns"></a>Retours

* `0`: Si la représentation longue du premier argument de chaîne IPv6 est égale au deuxième argument de chaîne IPv6.
* `1`: Si la représentation longue du premier argument de chaîne IPv6 est supérieure au deuxième argument de chaîne IPv6.
* `-1`: Si la représentation longue du premier argument de chaîne IPv6 est inférieure au deuxième argument de chaîne IPv6.
* `null`: Si la conversion de l’une des deux chaînes IPv6 a échoué.

## <a name="examples-ipv6ipv4-comparison-equality-cases"></a>Exemples : cas d’égalité de comparaison IPv6/IPv4

### <a name="compare-ips-using-the-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>Comparer des adresses IP à l’aide de la notation de préfixe IP spécifiée dans les chaînes IPv6/IPv4

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string)
[
 // IPv4 are compared as IPv6 addresses
 '192.168.1.1',    '192.168.1.1',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP4-prefix is used for comparison
  // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7994',         // Equal IPs
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7998/120',     // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998/120', // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1',      '::ffff:c0a8:0101', // Equal IPs
 '192.168.1.1/24',   '::ffff:c0a8:01ff', // 24 bit IP-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', // 24 bit IP-prefix is used for comparison
]
| extend result = ipv6_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|
|FE80 :: 85D : E82C : 9446:7994|FE80 :: 85D : E82C : 9446:7994|0|
|FE80 :: 85D : E82C : 9446:7994/120|FE80 :: 85D : E82C : 9446:7998|0|
|FE80 :: 85D : E82C : 9446:7994|FE80 :: 85D : E82C : 9446:7998/120|0|
|FE80 :: 85D : E82C : 9446:7994/120|FE80 :: 85D : E82C : 9446:7998/120|0|
|192.168.1.1|:: FFFF : c0a8:0101|0|
|192.168.1.1/24|:: FFFF : c0a8:01FF|0|
|:: FFFF : c0a8:0101|192.168.1.255/24|0|
|:: 192.168.1.1/30|192.168.1.255/24|0|

### <a name="compare-ips-using-ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_compare-function"></a>Comparez les adresses IP à l’aide de la notation de préfixe IP spécifiée dans les chaînes IPv6/IPv4 et en tant qu’argument supplémentaire de la `ipv6_compare()` fonction

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 // IPv4 are compared as IPv6 addresses 
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP4-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP4-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP4-prefix is used for comparison
   // IPv6 cases
 'fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995',     127, // 127 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7998', 120, // 120 bit IP6-prefix is used for comparison
 'fe80::85d:e82c:9446:7994/120', 'fe80::85d:e82c:9446:7998', 127, // 120 bit IP6-prefix is used for comparison
 // Mixed case of IPv4 and IPv6
 '192.168.1.1/24',   '::ffff:c0a8:01ff', 127, // 127 bit IP6-prefix is used for comparison
 '::ffff:c0a8:0101', '192.168.1.255',    120, // 120 bit IP6-prefix is used for comparison
 '::192.168.1.1/30', '192.168.1.255/24', 127, // 120 bit IP6-prefix is used for comparison
]
| extend result = ipv6_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|
|FE80 :: 85D : E82C : 9446:7994|FE80 :: 85D : E82C : 9446:7995|127|0|
|FE80 :: 85D : E82C : 9446:7994/127|FE80 :: 85D : E82C : 9446:7998|120|0|
|FE80 :: 85D : E82C : 9446:7994/120|FE80 :: 85D : E82C : 9446:7998|127|0|
|192.168.1.1/24|:: FFFF : c0a8:01FF|127|0|
|:: FFFF : c0a8:0101|192.168.1.255|120|0|
|:: 192.168.1.1/30|192.168.1.255/24|127|0|

