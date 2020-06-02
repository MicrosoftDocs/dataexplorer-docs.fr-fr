---
title: parse_ipv6 ()-Azure Explorateur de données
description: Cet article décrit la fonction parse_ipv6 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 05/27/2020
ms.openlocfilehash: d7858a67719bbde9a1ecedee5888abc7d0662e1e
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84301212"
---
# <a name="parse_ipv6"></a>parse_ipv6 ()

Convertit une chaîne IPv6 ou IPv4 en une représentation sous forme de chaîne IPv6 canonique.

```kusto
parse_ipv6("127.0.0.1") == '0000:0000:0000:0000:0000:ffff:7f00:0001'
parse_ipv6(":fe80::85d:e82c:9446:7994") == 'fe80:0000:0000:0000:085d:e82c:9446:7994'
```

**Syntaxe**

`parse_ipv6(`*`Expr`*`)`

**Arguments**

* *`Expr`*: Expression de chaîne représentant l’adresse réseau IPv6/IPv4 qui sera convertie en représentation IPv6 canonique. La chaîne peut inclure le masque net à l’aide [de la notation du préfixe IP](#ip-prefix-notation).

## <a name="ip-prefix-notation"></a>Notation de préfixe IP

Les adresses IP peuvent être définies à `IP-prefix notation` l’aide d’une barre oblique ( `/` ).
L’adresse IP à gauche de la barre oblique ( `/` ) est l’adresse IP de base. Le nombre (1 à 127) à droite de la barre oblique ( `/` ) est le nombre de 1 bits contigus dans le masque réseau.

**Retourne**

Si la conversion réussit, le résultat sera une chaîne représentant une adresse réseau IPv6 canonique.
Si la conversion échoue, le résultat sera `null` .

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string, netmask:long)
[
 '192.168.255.255',     32,  // 32-bit netmask is used
 '192.168.255.255/24',  30,  // 24-bit netmask is used, as IPv4 address doesn't use upper 8 bits
 '255.255.255.255',     24,  // 24-bit netmask is used
]
| extend ip_long = parse_ipv4_mask(ip_string, netmask)
```

|ip_string|masque réseau|ip_long|
|---|---|---|
|192.168.255.255|32|3232301055|
|192.168.255.255/24|30|3232300800|
|255.255.255.255|24|4294967040|

## <a name="next-steps"></a>Étapes suivantes

Pour d’autres fonctions similaires, consultez :

* [parse_ipv4()](parse-ipv4function.md)
* [ipv6_compare ()](ipv6-comparefunction.md)
* [ipv6_is_match ()](ipv6-is-matchfunction.md)
