---
title: parse_json() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit parse_json() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: f87c2155864f039fb5b261786d0fa6eb6770af2f
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744678"
---
# <a name="parse_json"></a>parse_json()

Interprète une `string` valeur JSON et retourne `dynamic`la valeur comme . 

Il est supérieur à l’utilisation [de l’extractjson () fonction](./extractjsonfunction.md) lorsque vous avez besoin d’extraire plus d’un élément d’un objet composé JSON.

**Syntaxe**

`parse_json(`*Json*`)`

Alias :
- [todynamic()](./todynamicfunction.md)
- [toobject()](./todynamicfunction.md)

**Arguments**

* *json*: Une `string`expression de type , représentant une [valeur formatée par JSON,](https://json.org/)ou une expression de type [dynamique,](./scalar-data-types/dynamic.md)représentant la valeur réelle. `dynamic`

**Retourne**

Un objet `dynamic` de type qui est déterminé par la valeur de *json*:
* Si *json* est `dynamic`de type, sa valeur est utilisée comme il est.
* Si *json* est `string`de type , et est une [chaîne JSON correctement formatée](https://json.org/), la chaîne est analysée et la valeur produite est retournée.
* Si *json* est `string`de type , mais ce **n’est pas** une chaîne [JSON correctement formatée](https://json.org/), alors la valeur retournée est un objet de type `dynamic` qui détient la valeur d’origine. `string`

**Exemple**

Dans l’exemple suivant, quand `context_custom_metrics` est un élément `string`, le résultat ressemble à ceci : 

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

puis le fragment CSL suivant récupère `duration` la valeur de la fente dans l’objet,`118.0` `110.0`et à partir de cela il récupère deux fentes, `duration.value` et `duration.min` ( et , respectivement).

```kusto
T
| extend d=parse_json(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```

**Remarques**

Il est quelque peu fréquent d’avoir une chaîne JSON décrivant un sac de propriété dans lequel l’un des "slots" est une autre chaîne JSON. Par exemple :

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d
```

Dans de tels cas, il `parse_json` est non seulement nécessaire d’invoquer `tostring` deux fois, mais aussi de s’assurer que dans le deuxième appel, sera utilisé. Sinon, le deuxième `parse_json` appel à transmettre simplement l’entrée à la sortie tel `dynamic`qu’il est, parce que son type déclaré est :

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```