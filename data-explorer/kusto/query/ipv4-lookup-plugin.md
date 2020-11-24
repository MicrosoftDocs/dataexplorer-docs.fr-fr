---
title: plug-in ipv4_lookup-Azure Explorateur de données
description: Cet article décrit ipv4_lookup plug-in dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/22/2020
ms.openlocfilehash: ec826e7d16e575fa4904aba93575ab7e605d2b18
ms.sourcegitcommit: 3af95ea6a6746441ac71b1a217bbb02ee23d5f28
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511146"
---
# <a name="ipv4_lookup-plugin"></a>plug-in ipv4_lookup

Le `ipv4_lookup` plug-in recherche une valeur IPv4 dans une table de recherche et retourne des lignes avec des valeurs correspondantes.

```kusto
T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey)

T | evaluate ipv4_lookup(LookupTable, SourceIPv4Key, LookupKey, return_unmatched = true)
```

## <a name="syntax"></a>Syntaxe

*T* `|` `evaluate` `ipv4_lookup(` *LookupTable* `,` *SourceIPv4Key* `,` *LookupKey* [ `,` *return_unmatched* ] `)`

## <a name="arguments"></a>Arguments

* *T*: entrée tabulaire dont la colonne *SourceIPv4Key* sera utilisée pour la correspondance IPv4.
* *LookupTable*: table ou expression tabulaire avec les données de recherche IPv4, dont la colonne *LookupKey* sera utilisée pour la correspondance IPv4. Les valeurs IPv4 peuvent être masquées à l’aide [de la notation de préfixe IP](#ip-prefix-notation).
* *SourceIPv4Key*: colonne de *T* avec chaîne IPv4 à rechercher dans *LookupTable*. Les valeurs IPv4 peuvent être masquées à l’aide [de la notation de préfixe IP](#ip-prefix-notation).
* *LookupKey*: colonne de *LookupTable* avec chaîne IPv4 qui correspond à chaque valeur *SourceIPv4Key* .
* *return_unmatched*: indicateur booléen qui définit si le résultat doit inclure toutes les lignes correspondantes ou uniquement (valeur par défaut : `false` seules les lignes correspondantes sont retournées).

### <a name="ip-prefix-notation"></a>Notation de préfixe IP
 
Les adresses IP peuvent être définies à `IP-prefix notation` l’aide d’une barre oblique ( `/` ).
L’adresse IP à gauche de la barre oblique ( `/` ) est l’adresse IP de base. Le nombre (1 à 32) à droite de la barre oblique ( `/` ) est le nombre de 1 bit contigu dans le masque réseau. 

Par exemple, 192.168.2.0/24 aura un net/Masque_Sous_réseau associé contenant 24 bits contigus ou 255.255.255.0 au format décimal avec points.

## <a name="returns"></a>Retours

Le `ipv4_lookup` plug-in renvoie un résultat de jointure (recherche) basé sur la clé IPv4. Le schéma de la table est l’Union de la table source et de la table de recherche, de la même façon que le résultat de l' [ `lookup` opérateur](lookupoperator.md).

Si l’argument *return_unmatched* est défini sur `true` , la table résultante inclut à la fois les lignes correspondantes et les lignes sans correspondance (remplies avec des valeurs null).

Si l’argument *return_unmatched* est défini sur `false` , ou s’il est omis (la valeur par défaut de `false` est utilisée), la table résultante aura autant d’enregistrements que résultats de correspondance. Cette variante de la recherche offre de meilleures performances par rapport à `return_unmatched=true` l’exécution.

> [!NOTE]
> * Ce plug-in couvre le scénario de jointure IPv4, en supposant une petite taille de table de recherche (100 000 à 200 000 lignes), la table d’entrée ayant éventuellement une taille supérieure.
> * Les performances du plug-in dépendent de la taille des tables de recherche et de la source de données, du nombre de colonnes et du nombre d’enregistrements correspondants.

## <a name="examples"></a>Exemples

### <a name="ipv4-lookup---matching-rows-only"></a>Recherche IPv4-lignes correspondantes uniquement

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string, continent_code:string ,continent_name:string, country_iso_code:string, country_name:string)
[
  "111.68.128.0/17","AS","Asia","JP","Japan",
  "5.8.0.0/19","EU","Europe","RU","Russia",
  "223.255.254.0/24","AS","Asia","SG","Singapore",
  "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
  "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
  '2.20.183.12',   // United Kingdom
  '5.8.1.2',       // Russia
  '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network)
```

|ip|réseau|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|Europe|Go|Royaume-Uni|
|5.8.1.2|5.8.0.0/19|EU|Europe|RU|Russie|

### <a name="ipv4-lookup---return-both-matching-and-non-matching-rows"></a>Recherche IPv4-retourner les lignes correspondantes et non correspondantes

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
// IP lookup table: IP_Data
// Partial data from: 
// https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv
let IP_Data = datatable(network:string,continent_code:string ,continent_name:string ,country_iso_code:string ,country_name:string )
[
    "111.68.128.0/17","AS","Asia","JP","Japan",
    "5.8.0.0/19","EU","Europe","RU","Russia",
    "223.255.254.0/24","AS","Asia","SG","Singapore",
    "46.36.200.51/32","OC","Oceania","CK","Cook Islands",
    "2.20.183.0/24","EU","Europe","GB","United Kingdom",
];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Unknown
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|réseau|continent_code|continent_name|country_iso_code|country_name|
|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|EU|Europe|Go|Royaume-Uni|
|5.8.1.2|5.8.0.0/19|EU|Europe|RU|Russie|
|192.165.12.17||||||

### <a name="ipv4-lookup---using-source-in-external_data"></a>Recherche IPv4-utilisation de la source dans external_data ()

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let IP_Data = external_data(network:string,geoname_id:long,continent_code:string,continent_name:string ,country_iso_code:string,country_name:string,is_anonymous_proxy:bool,is_satellite_provider:bool)
    ['https://raw.githubusercontent.com/datasets/geoip2-ipv4/master/data/geoip2-ipv4.csv'];
let IPs = datatable(ip:string)
[
    '2.20.183.12',   // United Kingdom
    '5.8.1.2',       // Russia
    '192.165.12.17', // Sweden
];
IPs
| evaluate ipv4_lookup(IP_Data, ip, network, return_unmatched = true)
```

|ip|réseau|geoname_id|continent_code|continent_name|country_iso_code|country_name|is_anonymous_proxy|is_satellite_provider|
|---|---|---|---|---|---|---|---|---|
|2.20.183.12|2.20.183.0/24|2635167|EU|Europe|Go|Royaume-Uni|0|0|
|5.8.1.2|5.8.0.0/19|2017370|EU|Europe|RU|Russie|0|0|
|192.165.12.17|192.165.8.0/21|2661886|EU|Europe|SE|Suède|0|0|
