---
title: ipv6_is_match ()-Azure Explorateur de données
description: Cet article décrit la fonction ipv6_is_match () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: d5bd270e016f1694c28f663a7fe8bf1d9a8f0903
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301280"
---
# <a name="ipv6_is_match"></a>ipv6_is_match ()

Correspond à deux chaînes d’adresse réseau IPv6 ou IPv4. Les deux chaînes IPv6/IPv4 sont analysées et comparées en tenant compte du masque de préfixe IP combiné calculé à partir des préfixes d’arguments et de l' `PrefixMask` argument facultatif.

```kusto
ipv6_is_match('::ffff:7f00:1', '127.0.0.1') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995') == false
ipv6_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv6_is_match('fe80::85d:e82c:9446:7994/127', 'fe80::85d:e82c:9446:7995/127') == true
ipv6_is_match('fe80::85d:e82c:9446:7994', 'fe80::85d:e82c:9446:7995', 127) == true
```

**Syntaxe**

`ipv6_is_match(`*Expr1* `, ` *Expr2* `[ ,` *PrefixMask*`])`

**Arguments**

* *Expr1*, *expr2*: expression de chaîne représentant une adresse IPv6 ou IPv4. Les chaînes IPv6 et IPv4 peuvent être masquées à l’aide [de la notation de préfixe IP](#ip-prefix-notation).
* *PrefixMask*: entier compris entre 0 et 128 représentant le nombre de bits les plus significatifs pris en compte.

## <a name="ip-prefix-notation"></a>Notation de préfixe IP
 
Les adresses IP peuvent être définies à `IP-prefix notation` l’aide d’une barre oblique ( `/` ).
L’adresse IP à gauche de la barre oblique ( `/` ) est l’adresse IP de base. Le nombre (1 à 127) à droite de la barre oblique ( `/` ) est le nombre de 1 bit contigu dans le masque réseau. 

**Exemple**: fe80 :: 85D : E82C : 9446:7994/120 aura un net/Masque_Sous_réseau associé contenant 120 bits contigus.

**Retourne**

* `true`: Si la représentation longue du premier argument de chaîne IPv6/IPv4 est égale au deuxième argument de chaîne IPv6/IPv4.
* `false`Dispose.
* `null`: Si la conversion de l’une des deux chaînes IPv6/IPv4 a échoué.

> [!Note]
> La fonction peut accepter et comparer des arguments qui représentent à la fois des adresses réseau IPv6 et IPv4. Si l’appelant sait que les arguments sont au format IPv4, utilisez la fonction [ipv4_is_match ()](./ipv4-is-matchfunction.md) . Cette fonction permet d’obtenir de meilleures performances d’exécution.

## <a name="examples"></a>Exemples

### <a name="ipv6ipv4-comparison-equality-case---ip-prefix-notation-specified-inside-the-ipv6ipv4-strings"></a>Cas d’égalité de comparaison IPv6/IPv4-notation IP-préfixe spécifiée dans les chaînes IPv6/IPv4

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
| extend result = ipv6_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.1|192.168.1.1|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|
|FE80 :: 85D : E82C : 9446:7994|FE80 :: 85D : E82C : 9446:7994|1|
|FE80 :: 85D : E82C : 9446:7994/120|FE80 :: 85D : E82C : 9446:7998|1|
|FE80 :: 85D : E82C : 9446:7994|FE80 :: 85D : E82C : 9446:7998/120|1|
|FE80 :: 85D : E82C : 9446:7994/120|FE80 :: 85D : E82C : 9446:7998/120|1|
|192.168.1.1|:: FFFF : c0a8:0101|1|
|192.168.1.1/24|:: FFFF : c0a8:01FF|1|
|:: FFFF : c0a8:0101|192.168.1.255/24|1|
|:: 192.168.1.1/30|192.168.1.255/24|1|


### <a name="ipv6ipv4-comparison-equality-case--ip-prefix-notation-specified-inside-the-ipv6ipv4-strings-and-as-additional-argument-of-the-ipv6_is_match-function"></a>Cas d’égalité de comparaison IPv6/IPv4 : notation de préfixe IP spécifiée dans les chaînes IPv6/IPv4 et argument supplémentaire de la `ipv6_is_match()` fonction

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
| extend result = ipv6_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|
|FE80 :: 85D : E82C : 9446:7994|FE80 :: 85D : E82C : 9446:7995|127|1|
|FE80 :: 85D : E82C : 9446:7994/127|FE80 :: 85D : E82C : 9446:7998|120|1|
|FE80 :: 85D : E82C : 9446:7994/120|FE80 :: 85D : E82C : 9446:7998|127|1|
|192.168.1.1/24|:: FFFF : c0a8:01FF|127|1|
|:: FFFF : c0a8:0101|192.168.1.255|120|1|
|:: 192.168.1.1/30|192.168.1.255/24|127|1|
