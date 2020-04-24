---
title: cas() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit le cas () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 479c4a99d410a2df7a608531914d7dccfc555e7e
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81517270"
---
# <a name="case"></a>case()

Évalue une liste de prédicats et renvoie la première expression de résultat dont le prédicat est satisfait.

Si aucun des prédicats retour `true`, le résultat `else`de la dernière expression (le ) est retourné.
Tous les arguments impairs (compter commence à 1) doivent être des expressions qui évaluent à une `boolean` valeur.
Tous les arguments `then`(le s) et `else`le dernier argument (le ) doit être du même type.

**Syntaxe**

`case(`*predicate_1* *then_1*, *predicate_2* `,` *then_2*, *predicate_3* `,` *then_3*, *autre* `,``)`

**Arguments**

* *predicate_i*: Une expression qui évalue `boolean` à une valeur.
* *then_i*: Une expression qui est évaluée et sa valeur *predicate_i* est renvoyée de la fonction si `true`predicate_i est le premier prédicat qui évalue à .
* *autre*: Une expression qui est évaluée et sa valeur *predicate_i* est renvoyée de `true`la fonction si aucun des predicate_i évaluer à .

**Retourne**

La valeur de la première *then_i* dont *le* predicate_i `true`évalue à , ou la valeur *d’autre* si aucun des prédicats sont satisfaits.

**Exemple**

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
