---
title: ipv4_is_match ()-Azure Explorateur de données
description: Cet article décrit ipv4_is_match () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: b63a73efe73223ba7c6bd2b42c6e05a6c60e94b6
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271449"
---
# <a name="ipv4_is_match"></a>ipv4_is_match ()

Correspond à deux chaînes IPv4.

```kusto
ipv4_is_match("127.0.0.1", "127.0.0.1") == true
ipv4_is_match('192.168.1.1', '192.168.1.255') == false
ipv4_is_match('192.168.1.1/24', '192.168.1.255/24') == true
ipv4_is_match('192.168.1.1', '192.168.1.255', 24) == true
```

**Syntaxe**

`ipv4_is_match(`*Expr1* `, ` *Expr2* `[ ,` *PrefixMask*`])`

**Arguments**

* *Expr1*, *expr2*: expression de chaîne représentant une adresse IPv4. Les chaînes IPv4 peuvent être masquées à l’aide [de la notation de préfixe IP](#ip-prefix-notation).
* *PrefixMask*: entier compris entre 0 et 32 représentant le nombre de bits les plus significatifs pris en compte.

### <a name="ip-prefix-notation"></a>Notation de préfixe IP

Il est courant de définir des adresses IP à l’aide d' `IP-prefix notation` une barre oblique ( `/` ). L’adresse IP à gauche de la barre oblique ( `/` ) est l’adresse IP de base, et le nombre (de 1 à 32) à droite de la barre oblique ( `/` ) est le nombre de 1 bits contigus dans le masque réseau. 

Exemple : 192.168.2.0/24 aura un net/Masque_Sous_réseau associé contenant 24 bits contigus ou 255.255.255.0 au format décimal avec points.

**Retourne**

Les deux chaînes IPv4 sont analysées et comparées en tenant compte du masque de préfixe IP combiné calculé à partir des préfixes d’arguments et de l' `PrefixMask` argument facultatif.

Retourne les informations suivantes :
* `true`: Si la représentation longue du premier argument de chaîne IPv4 est égale au deuxième argument de chaîne IPv4.
*  `false`Dispose.

Si la conversion de l’une des deux chaînes IPv4 a échoué, le résultat sera `null` .

**Exemples**

## <a name="ipv4-comparison-equality-cases"></a>Cas d’égalité de comparaison IPv4

L’exemple suivant compare différentes adresses IP à l’aide de la notation de préfixe IP spécifiée dans les chaînes IPv4.

<!-- csl: https://help.kusto.windows.net/Samples -->
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

L’exemple suivant compare différentes adresses IP à l’aide de la notation de préfixe IP spécifiée dans les chaînes IPv4 et en tant qu’argument supplémentaire de la `ipv4_is_match()` fonction.

<!-- csl: https://help.kusto.windows.net/Samples -->
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
