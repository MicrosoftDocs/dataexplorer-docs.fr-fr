---
title: parse_ipv4() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit parse_ipv4() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 296c7e77a2397501ae086e16154ca249249c23e4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81511745"
---
# <a name="parse_ipv4"></a>parse_ipv4()

Convertit la chaîne IPv4 en représentation longue (signée 64 bits).

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**Syntaxe**

`parse_ipv4(`*Expr*`)`

**Arguments**

* *Expr*: Expression de cordes représentant IPv4 qui sera convertie en long. La chaîne peut inclure le masque net à l’aide [de la notation IP-prefix](#ip-prefix-notation).

### <a name="ip-prefix-notation"></a>Notation IP-prefix

Il est courant de définir `IP-prefix notation` les adresses`/`IP à l’aide d’un caractère slash ( ) .
L’adresse IP à la`/`LEFT de la barre oblique ( ) est l’adresse IP de base, et le nombre (1 à 32) à la droite de la barre oblique (/) est le nombre de contigus 1 bits dans le netmask. Ainsi, 192.168.2.0/24 aura un filet/sous-filet associé contenant 24 bits contigus ou 255.255.255.0 en format décimale pointillé.

**Retourne**

Si la conversion est réussie, le résultat sera un nombre long.
Si la conversion n’est `null`pas réussie, le résultat sera .
 
**Exemple**

```kusto
datatable(ip_string:string)
[
 '192.168.1.1',
 '192.168.1.1/24',
 '255.255.255.255/31'
]
| extend ip_long = parse_ipv4(ip_string)
```

|ip_string|ip_long|
|---|---|
|192.168.1.1|3232235777|
|192.168.1.1/24|3232235776|
|255.255.255.255/31|4294967294|