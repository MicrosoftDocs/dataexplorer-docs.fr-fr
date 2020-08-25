---
title: dynamic_to_json ()-Azure Explorateur de données
description: Cet article décrit dynamic_to_json () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 08/05/2020
ms.openlocfilehash: 94901a69b4c4435e983f39f1692cf45cf27756e5
ms.sourcegitcommit: 05489ce5257c0052aee214a31562578b0ff403e7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88793785"
---
# <a name="dynamic_to_json"></a>dynamic_to_json()

Convertit l' `dynamic` entrée en une représentation sous forme de chaîne.
Si l’entrée est un conteneur de propriétés, la chaîne de sortie imprime son contenu trié par les clés de manière récursive. Dans le cas contraire, la sortie est similaire à la sortie de la `tostring` fonction.

## <a name="syntax"></a>Syntaxe

`dynamic_to_json(Expr)`

## <a name="arguments"></a>Arguments

* *Expr*: `dynamic` entrée. La fonction accepte un seul argument.

## <a name="returns"></a>Retours

Retourne une représentation sous forme de chaîne de l' `dynamic` entrée. Si l’entrée est un conteneur de propriétés, la chaîne de sortie imprime son contenu trié par les clés de manière récursive.

## <a name="examples"></a>Exemples

Expression :

```kusto
  let bag1 = dynamic_to_json(dynamic({ 'Y10':dynamic({ }), 'X8': dynamic({ 'c3':1, 'd8':5, 'a4':6 }),'D1':114, 'A1':12, 'B1':2, 'C1':3, 'A14':[15, 13, 18]}));
  print bag1
```
  
Résultat :

```
"{
  ""A1"": 12,
  ""A14"": [
    15,
    13,
    18
  ],
  ""B1"": 2,
  ""C1"": 3,
  ""D1"": 114,
  ""X8"": {
    ""c3"": 1,
    ""d8"": 5,
    ""a4"": 6
  },
  ""Y10"": {}
}"
```

Expression :

```kusto
 let bag2 = dynamic_to_json(dynamic({ 'X8': dynamic({ 'a4':6, 'c3':1, 'd8':5}), 'A14':[15, 13, 18], 'C1':3, 'B1':2, 'Y10': dynamic({ }), 'A1':12, 'D1':114}));
 print bag2
```
 
Résultat :

```
{
  "A1": 12,
  "A14": [
    15,
    13,
    18
  ],
  "B1": 2,
  "C1": 3,
  "D1": 114,
  "X8": {
    "a4": 6,
    "c3": 1,
    "d8": 5
  },
  "Y10": {}
}
```
