---
title: case ()-Azure Explorateur de données
description: Cet article décrit la casse () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 809c11f337db86e9b9bdfbd93439e5f743008c38
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92249319"
---
# <a name="case"></a>case()

Évalue une liste de prédicats et retourne la première expression de résultat dont le prédicat est respecté.

Si aucun des prédicats `true` n’est retourné, le résultat de la dernière expression ( `else` ) est retourné.
Tous les arguments impairs (le nombre commence à 1) doivent être des expressions qui correspondent à une  `boolean` valeur.
Tous les arguments pairs (les objets `then` ) et le dernier argument ( `else` ) doivent être du même type.

## <a name="syntax"></a>Syntaxe

`case(`*predicate_1*, *then_1*, *predicate_2*, *then_2*, *predicate_3*, *then_3*, *sinon*`)`

## <a name="arguments"></a>Arguments

* *predicate_i*: expression qui prend une `boolean` valeur.
* *then_i*: expression qui est évaluée et sa valeur est retournée à partir de la fonction si *predicate_i* est le premier prédicat qui prend la valeur `true` .
* *else*: expression qui est évaluée et sa valeur est retournée à partir de la fonction si aucune des *predicate_i* ne correspond à `true` .

## <a name="returns"></a>Retours

La valeur du premier *then_i* dont *predicate_i* prend `true` la valeur, ou la valeur de *else* si aucun des prédicats n’est respecté.

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range Size from 1 to 15 step 2
| extend bucket = case(Size <= 3, "Small", 
                       Size <= 10, "Medium", 
                       "Large")
```

|Taille|Compartiment|
|---|---|
|1|Petite|
|3|Petite|
|5|Moyenne|
|7|Moyenne|
|9|Moyenne|
|11|grand|
|13|grand|
|15|grand|
