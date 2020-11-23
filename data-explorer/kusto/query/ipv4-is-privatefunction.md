---
title: ipv4_is_private ()-Azure Explorateur de données
description: Cet article décrit la fonction ipv4_is_private () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/20/2020
ms.openlocfilehash: 161e0a2ca94bb9b037fc619c0fc5dfbddd8d28da
ms.sourcegitcommit: 4c7f20dfd59fb5b5b1adfbbcbc9b7da07df5e479
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95324899"
---
# <a name="ipv4_is_private"></a>ipv4_is_private ()

Vérifie si l’adresse de chaîne IPv4 appartient à un ensemble d’adresses IP de réseau privé.

Les [adresses de réseau privé](https://en.wikipedia.org/wiki/Private_network) ont été initialement définies pour aider à retarder l’épuisement des adresses IPv4. Les paquets IP provenant de ou adressés à une adresse IP privée ne peuvent pas être acheminés via l’Internet public.

## <a name="private-ipv4-addresses"></a>Adresses IPv4 privées

L’IETF (Internet Engineering Task Force) a demandé à l’IANA (Internet Assigned Numbers Authority) de réserver les plages d’adresses IPv4 suivantes pour les réseaux privés :

| Plage d'adresses IP|Nombre d’adresses|Bloc CIDR le plus volumineux (masque de sous-réseau)|
|-----------------|-------------------|--------------------------------|
|10.0.0.0 – 10.255.255.255|16 777 216|10.0.0.0/8 (255.0.0.0)|
|172.16.0.0 – 172.31.255.255|1 048 576|172.16.0.0/12 (255.240.0.0)|
|192.168.0.0 – 192.168.255.255|65536|192.168.0.0/16 (255.255.0.0)|


```kusto
ipv4_is_private('192.168.1.1/24') == true
ipv4_is_private('10.1.2.3/24') == true
ipv4_is_private('202.1.2.3') == false
ipv4_is_private("127.0.0.1") == false
```

## <a name="syntax"></a>Syntaxe

`ipv4_is_private(`*Expr*`)`

## <a name="arguments"></a>Arguments

*Expr*: expression de chaîne représentant une adresse IPv4. Les chaînes IPv4 peuvent être masquées à l’aide [de la notation de préfixe IP](#ip-prefix-notation).

### <a name="ip-prefix-notation"></a>Notation de préfixe IP

Les adresses IP peuvent être définies à `IP-prefix notation` l’aide d’une barre oblique ( `/` ). L’adresse IP à gauche de la barre oblique ( `/` ) est l’adresse IP de base. Le nombre (1 à 32) à droite de la barre oblique ( `/` ) est le nombre de 1 bit contigu dans le masque réseau. 

Par exemple, 192.168.2.0/24 aura un net/Masque_Sous_réseau associé contenant 24 bits contigus ou 255.255.255.0 au format décimal avec points.

## <a name="returns"></a>Retours

* `true`: Si l’adresse IPv4 appartient à l’une des plages de réseau privé.
*  `false`Dispose.
* `null`: Si la conversion de l’une des deux chaînes IPv4 a échoué.

## <a name="example-check-if-ipv4-belongs-to-a-private-network"></a>Exemple : vérifier si IPv4 appartient à un réseau privé

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(ip_string:string)
[
 '10.1.2.3',
 '192.168.1.1/24',
 '127.0.0.1',
]
| extend result = ipv4_is_private(ip_string)
```

|ip_string|result|
|---|---|
|10.1.2.3|1|
|192.168.1.1/24|1|
|127.0.0.1|0|
