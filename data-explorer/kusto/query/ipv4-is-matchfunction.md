---
title: ipv4_is_match() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit ipv4_is_match() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: aa0646321af2d467c1e4af07fba81ccdc58eff37
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81513734"
---
# <a name="ipv4_is_match"></a>ipv4_is_match()

Correspond à deux cordes IPv4.

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

**Syntaxe**

`ipv4_is_match(`*Expr1*`, `Expr2 PrefixMask Expr2 PrefixMask Expr1*PrefixMask* *Expr1*`[ ,``])`

**Arguments**

* *Expr1*, *Expr2*: Une expression de chaîne représentant une adresse IPv4. Les chaînes IPv4 peuvent être masquées à l’aide [de la notation IP-préfixe.](#ip-prefix-notation)
* *PrefixMask*: Un intégré de 0 à 32 représentant le nombre de bits les plus significatifs qui sont pris en compte.

### <a name="ip-prefix-notation"></a>Notation IP-prefix

Il est courant de définir `IP-prefix notation` les adresses`/`IP à l’aide d’un caractère slash ( ) . L’adresse IP à la`/`LEFT de la barre oblique ( ) est l’adresse IP de`/`base, et le nombre (1 à 32) à la droite de la barre oblique ( ) est le nombre de contigus 1 bits dans le netmask. 

Exemple : 192.168.2.0/24 aura un filet/sous-filet associé contenant 24 bits contigus ou 255.255.255.0 en format décimale pointillé.

**Retourne**

Les deux chaînes IPv4 sont analysées et comparées tout en tenant compte du masque combiné `PrefixMask` IP-préfixe calculé à partir de préfixes d’argument, et de l’argument facultatif.

Retourne les informations suivantes :
* `true`: Si la longue représentation du premier argument de chaîne IPv4 est égale à la deuxième argument de chaîne IPv4.
*  `false`: Sinon.

Si la conversion pour l’une des deux chaînes IPv4 n’a pas été couronnée de succès, le résultat sera `null`.

**Exemples**

## <a name="ipv4-comparison-equality-cases"></a>IPv4 cas d’égalité de comparaison

L’exemple suivant compare divers IP à l’aide de la notation IP-préfixe spécifiée à l’intérieur des chaînes IPv4.

```kusto
datatable(ip1_string:string, ip2_string:string)
[
 '192.168.1.0',    '192.168.1.0',       // Equal IPs
 '192.168.1.1/24', '192.168.1.255',     // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
 '192.168.1.1/30', '192.168.1.255/24',  // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|1|
|192.168.1.1/24|192.168.1.255|1|
|192.168.1.1|192.168.1.255/24|1|
|192.168.1.1/30|192.168.1.255/24|1|

L’exemple suivant compare divers IP à l’aide de la notation IP-préfixe spécifiée à l’intérieur des chaînes IPv4 et comme argument supplémentaire de la `ipv4_is_match()` fonction.

```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_is_match(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|1|
|192.168.1.1/24|192.168.1.255|31|1|
|192.168.1.1|192.168.1.255|24|1|