---
title: parse_ipv4 ()-Azure Explorateur de données
description: Cet article décrit parse_ipv4 () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 459b94d4fdb8dbd9d294367b2cee49aab9800406
ms.sourcegitcommit: 41cd88acc1fd79f320a8fe8012583d4c8522db78
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84294591"
---
# <a name="parse_ipv4"></a>parse_ipv4()

Convertit une chaîne IPv4 en une représentation de nombre longue (signée 64 bits).

```kusto
parse_ipv4("127.0.0.1") == 2130706433
parse_ipv4('192.1.168.1') < parse_ipv4('192.1.168.2') == true
```

**Syntaxe**

`parse_ipv4(`*`Expr`*`)`

**Arguments**

* *`Expr`*: Expression de chaîne représentant IPv4 qui sera convertie en long. La chaîne peut inclure le masque net à l’aide [de la notation du préfixe IP](#ip-prefix-notation).

## <a name="ip-prefix-notation"></a>Notation de préfixe IP

Les adresses IP peuvent être définies à `IP-prefix notation` l’aide d’une barre oblique ( `/` ).
L’adresse IP à gauche de la barre oblique ( `/` ) est l’adresse IP de base. Le nombre (1 à 32) à droite de la barre oblique (/) est le nombre de 1 bit contigu dans le masque réseau. 

**Exemple :** 192.168.2.0/24 aura un net/Masque_Sous_réseau associé contenant 24 bits contigus ou 255.255.255.0 au format décimal avec points.

**Retourne**

Si la conversion réussit, le résultat sera un nombre long.
Si la conversion échoue, le résultat sera `null` .
 
**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples -->
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
