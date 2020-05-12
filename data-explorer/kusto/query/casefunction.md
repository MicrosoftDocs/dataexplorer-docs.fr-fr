---
title: case ()-Azure Explorateur de données
description: Cet article décrit la casse () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: b493f74472454649a557b7e3677b26af169413de
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227551"
---
# <a name="case"></a>case()

Évalue une liste de prédicats et retourne la première expression de résultat dont le prédicat est respecté.

Si aucun des prédicats `true` n’est retourné, le résultat de la dernière expression ( `else` ) est retourné.
Tous les arguments impairs (le nombre commence à 1) doivent être des expressions qui correspondent à une `boolean` valeur.
Tous les arguments pairs (les objets `then` ) et le dernier argument ( `else` ) doivent être du même type.

**Syntaxe**

`case(`*predicate_1* `,` *then_1*, *predicate_2* `,` *then_2*, *predicate_3* `,` *then_3*, *sinon*`)`

**Arguments**

* *predicate_i*: expression qui prend une `boolean` valeur.
* *then_i*: expression qui est évaluée et sa valeur est retournée à partir de la fonction si *predicate_i* est le premier prédicat qui prend la valeur `true` .
* *else*: expression qui est évaluée et sa valeur est retournée à partir de la fonction si aucune des *predicate_i* ne correspond à `true` .

**Retourne**

La valeur du premier *then_i* dont *predicate_i* prend `true` la valeur, ou la valeur de *else* si aucun des prédicats n’est respecté.

**Exemple**

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
