---
title: parse_json ()-Azure Explorateur de données
description: Cet article décrit parse_json () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: abe49795b7b997abf677fd0fafff10ae38787f44
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346335"
---
# <a name="parse_json"></a>parse_json()

Interprète un `string` comme une valeur JSON et retourne la valeur sous la forme `dynamic` .

Cette fonction est préférable à la [fonction extractjson ()](./extractjsonfunction.md) lorsque vous devez extraire plus d’un élément d’un objet composé JSON.

## <a name="syntax"></a>Syntaxe

`parse_json(`*json*`)`

Alias :
- [todynamic()](./todynamicfunction.md)
- [toobject()](./todynamicfunction.md)

## <a name="arguments"></a>Arguments

* *JSON*: expression de type `string` . Il représente une [valeur au format JSON](https://json.org/), ou une expression de type [Dynamic](./scalar-data-types/dynamic.md), représentant la `dynamic` valeur réelle.

## <a name="returns"></a>Retourne

Objet de type `dynamic` qui est déterminé par la valeur de *JSON*:
* Si *JSON* est de type `dynamic` , sa valeur est utilisée telle quelle.
* Si *JSON* est de type `string` et qu’il s’agit d’une [chaîne JSON correctement mise en forme](https://json.org/), la chaîne est analysée et la valeur produite est retournée.
* Si *JSON* est de type `string` , mais qu’il ne s’agit pas d’une [chaîne JSON correctement mise en forme](https://json.org/), la valeur retournée est un objet de type `dynamic` qui contient la valeur d’origine `string` .

## <a name="example"></a>Exemple

Dans l’exemple suivant, quand `context_custom_metrics` est un élément `string`, le résultat ressemble à ceci :

```json
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

Ensuite, le fragment CSL suivant récupère la valeur de l' `duration` emplacement dans l’objet et, à partir de là, il récupère deux emplacements, `duration.value` et `duration.min` ( `118.0` `110.0` respectivement).

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

Dans ce cas, il n’est pas nécessaire d’appeler `parse_json` deux fois, mais également de s’assurer que dans le deuxième appel, `tostring` est utilisé. Dans le cas contraire, le deuxième appel à `parse_json` passera simplement l’entrée à la sortie telle quelle, car son type déclaré est `dynamic` .

```kusto
let d='{"a":123, "b":"{\\"c\\":456}"}';
print d_b_c=parse_json(tostring(parse_json(d).b)).c
```
