---
title: ipv4_compare ()-Azure Explorateur de données
description: Cet article décrit ipv4_compare () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 9fec1869ee06e4fd9a9932e42c6ab1049b50a04f
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271500"
---
# <a name="ipv4_compare"></a>ipv4_compare ()

Compare deux chaînes IPv4.

```kusto
ipv4_compare("127.0.0.1", "127.0.0.1") == 0
ipv4_compare('192.168.1.1', '192.168.1.255') < 0
ipv4_compare('192.168.1.1/24', '192.168.1.255/24') == 0
ipv4_compare('192.168.1.1', '192.168.1.255', 24) == 0
```

**Syntaxe**

`ipv4_compare(`*Expr1* `, ` *Expr2* `[ ,` *PrefixMask*`])`

**Arguments**

* *Expr1*, *expr2*: expression de chaîne représentant une adresse IPv4. Les chaînes IPv4 peuvent être masquées à l’aide [de la notation de préfixe IP](#ip-prefix-notation).
* *PrefixMask*: entier compris entre 0 et 32 représentant le nombre de bits les plus significatifs pris en compte.

### <a name="ip-prefix-notation"></a>Notation de préfixe IP

Il est courant de définir des adresses IP à l’aide d' `IP-prefix notation` une barre oblique ( `/` ).
L’adresse IP à gauche de la barre oblique ( `/` ) est l’adresse IP de base, et le nombre (de 1 à 32) à droite de la barre oblique ( `/` ) est le nombre de 1 bits contigus dans le masque réseau. 

Exemple : 192.168.2.0/24 aura un net/Masque_Sous_réseau associé contenant 24 bits contigus ou 255.255.255.0 au format décimal avec points.

**Retourne**

Les deux chaînes IPv4 sont analysées et comparées en tenant compte du masque de préfixe IP combiné calculé à partir des préfixes d’arguments et de l' `PrefixMask` argument facultatif.

Retourne les informations suivantes :
* `0`: Si la représentation longue du premier argument de chaîne IPv4 est égale au deuxième argument de chaîne IPv4
* `1`: Si la représentation longue du premier argument de chaîne IPv4 est supérieure au deuxième argument de chaîne IPv4
* `-1`: Si la représentation longue du premier argument de chaîne IPv4 est inférieure au deuxième argument de chaîne IPv4

Si la conversion de l’une des deux chaînes IPv4 a échoué, le résultat sera `null` .

## <a name="examples-ipv4-comparison-equality-cases"></a>Exemples : cas d’égalité de comparaison IPv4

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
| extend result = ipv4_compare(ip1_string, ip2_string)
```

|ip1_string|ip2_string|result|
|---|---|---|
|192.168.1.0|192.168.1.0|0|
|192.168.1.1/24|192.168.1.255|0|
|192.168.1.1|192.168.1.255/24|0|
|192.168.1.1/30|192.168.1.255/24|0|

L’exemple suivant compare différentes adresses IP à l’aide de la notation de préfixe IP spécifiée dans les chaînes IPv4 et en tant qu’argument supplémentaire de la `ipv4_compare()` fonction.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip1_string:string, ip2_string:string, prefix:long)
[
 '192.168.1.1',    '192.168.1.0',   31, // 31 bit IP-prefix is used for comparison
 '192.168.1.1/24', '192.168.1.255', 31, // 24 bit IP-prefix is used for comparison
 '192.168.1.1',    '192.168.1.255', 24, // 24 bit IP-prefix is used for comparison
]
| extend result = ipv4_compare(ip1_string, ip2_string, prefix)
```

|ip1_string|ip2_string|prefix|result|
|---|---|---|---|
|192.168.1.1|192.168.1.0|31|0|
|192.168.1.1/24|192.168.1.255|31|0|
|192.168.1.1|192.168.1.255|24|0|
