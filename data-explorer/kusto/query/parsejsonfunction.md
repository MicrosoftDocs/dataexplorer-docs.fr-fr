---
title: Fonctions todynamic(), parse_json() - Azure Data Explorer
description: Cet article décrit les fonctions todynamic(), parse_json() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 01/25/2021
ms.localizationpriority: high
ms.openlocfilehash: 680ce93ec2a3b39d14475689ac80793a02d86547
ms.sourcegitcommit: 8468c6886dc097032ef6db86992a5e8c9b18786e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98911942"
---
# <a name="todynamic-parse_json"></a>todynamic(), parse_json()

Interprète une `string` comme une valeur JSON et retourne la valeur en tant que `dynamic`. 

> [!NOTE]
> Les fonctions `todynamic()` et `parse_json()` sont interprétées de façon équivalente.

Cette fonction est une meilleure option que la [fonction extractjson()](./extractjsonfunction.md) si vous devez extraire plusieurs éléments d’un objet composite JSON. Préférez l’utilisation de [dynamic()](./scalar-data-types/dynamic.md) lorsque cela est possible.

## <a name="syntax"></a>Syntaxe

`parse_json(`*json*`)`
`todynamic(`*json*`)`

<!-- deprecated aliases: `toobject()` and parsejson() -->

## <a name="arguments"></a>Arguments

* *json* : expression de type `string`. Elle représente une [valeur au format JSON](https://json.org/) ou une expression de type [dynamique](./scalar-data-types/dynamic.md), représentant la valeur `dynamic` réelle.

## <a name="returns"></a>Retours

Objet de type `dynamic` déterminé par la valeur de *json* :
* Si *json* est de type `dynamic`, sa valeur est utilisée telle quelle.
* Si *json* est de type `string`et qu’il s’agit d’une [chaîne JSON correctement mise en forme](https://json.org/), la chaîne est analysée et la valeur produite est retournée.
* Si *json* est de type `string`, mais qu’il ne s’agit pas d’une [chaîne JSON correctement mise en forme](https://json.org/), la valeur retournée est un objet de type `dynamic` qui contient la valeur `string` d’origine.

## <a name="example"></a>Exemple

Dans l’exemple suivant, quand `context_custom_metrics` est un élément `string`, le résultat ressemble à ceci :

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

Le fragment CSL suivant récupère la valeur de l’emplacement `duration` dans l’objet et, grâce à cette valeur, récupère deux emplacements, `duration.value` et `duration.min` (`118.0` et `110.0`, respectivement).

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**Remarques**

Il est courant d’avoir une chaîne JSON qui décrit un conteneur de propriétés dans lequel l’un des « emplacements » est une autre chaîne JSON. 

Par exemple :

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

Dans ce cas, il est nécessaire d’appeler `parse_json` deux fois, et également de s’assurer que dans le deuxième appel, `tostring` est utilisé. Sinon, le deuxième appel à `parse_json` passe simplement l’entrée à la sortie telle quelle, car son type déclaré est `dynamic`.

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
