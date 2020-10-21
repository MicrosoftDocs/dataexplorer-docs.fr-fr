---
title: parse_version ()-Azure Explorateur de données
description: Cet article décrit parse_version () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cae7fe9a46d885555f1b28d9282962319fd51b87
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246227"
---
# <a name="parse_version"></a>parse_version()

Convertit la représentation sous forme de chaîne d’entrée d’une version en nombre décimal comparable.

```kusto
parse_version("0.0.0.1")
```

## <a name="syntax"></a>Syntaxe

`parse_version``(` *Expr*`)`

## <a name="arguments"></a>Arguments

* *`Expr`*: Expression scalaire de type `string` qui spécifie la version à analyser.

> [!NOTE]
> * La chaîne d’entrée doit contenir entre une et quatre parties de version, représentées sous forme de nombres et séparées par des points ('. ').
> * Chaque partie de la version peut contenir jusqu’à huit chiffres, avec une valeur maximale de 99999999.
> * Si le nombre de parties est inférieur à quatre, toutes les parties manquantes sont considérées comme à la fin ( `1.0`  ==  `1.0.0.0` ).

## <a name="returns"></a>Retours

Si la conversion réussit, le résultat sera un nombre décimal.
Si la conversion échoue, le résultat sera `null` .

## <a name="example"></a>Exemple

```kusto
let dt = datatable(v:string)
["0.0.0.5","0.0.7.0","0.0.3","0.2","0.1.2.0","1.2.3.4","1","99999999.0.0.0"];
dt | project v1=v, _key=1 
| join kind=inner (dt | project v2=v, _key = 1) on _key | where v1 != v2
| summarize v1 = max(v1),v2 = min(v2) by (hash(v1) + hash(v2)) // removing duplications
| project v1, v2, higher_version = iif(parse_version(v1) > parse_version(v2), v1, v2)

```

|v1|v2|higher_version|
|---|---|---|
|99999999.0.0.0|0.0.0.5|99999999.0.0.0|
|1|0.0.0.5|1|
|1.2.3.4|0.0.0.5|1.2.3.4|
|0.1.2.0|0.0.0.5|0.1.2.0|
|0.2|0.0.0.5|0.2|
|0.0.3|0.0.0.5|0.0.3|
|0.0.7.0|0.0.0.5|0.0.7.0|
|99999999.0.0.0|0.0.7.0|99999999.0.0.0|
|1|0.0.7.0|1|
|1.2.3.4|0.0.7.0|1.2.3.4|
|0.1.2.0|0.0.7.0|0.1.2.0|
|0.2|0.0.7.0|0.2|
|0.0.7.0|0.0.3|0.0.7.0|
|99999999.0.0.0|0.0.3|99999999.0.0.0|
|1|0.0.3|1|
|1.2.3.4|0.0.3|1.2.3.4|
|0.1.2.0|0.0.3|0.1.2.0|
|0.2|0.0.3|0.2|
|99999999.0.0.0|0.2|99999999.0.0.0|
|1|0.2|1|
|1.2.3.4|0.2|1.2.3.4|
|0.2|0.1.2.0|0.2|
|99999999.0.0.0|0.1.2.0|99999999.0.0.0|
|1|0.1.2.0|1|
|1.2.3.4|0.1.2.0|1.2.3.4|
|99999999.0.0.0|1.2.3.4|99999999.0.0.0|
|1.2.3.4|1|1.2.3.4|
|99999999.0.0.0|1|99999999.0.0.0|
