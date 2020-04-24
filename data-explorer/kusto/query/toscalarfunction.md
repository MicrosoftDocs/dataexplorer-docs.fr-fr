---
title: toscalar() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit toscalar() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 60fe8123760a9921bfa7abfacbbdffba6dba8d7b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81505897"
---
# <a name="toscalar"></a>toscalar()

Retourne une valeur constante scalaire de l’expression évaluée. 

Cette fonction est utile pour les requêtes qui nécessitent des calculs par étapes, comme par exemple le calcul d’un nombre total d’événements, puis l’utiliser pour les groupes de filtrage qui dépassent certains pour cent de tous les événements. 

**Syntaxe**

`toscalar(`*Expression*`)`

**Arguments**

* *Expression*: Expression qui sera évaluée pour la conversion scalar  

**Retourne**

Une valeur constante scalaire de l’expression évaluée.
Si le résultat d’expression est un tabulaire, alors la première colonne et la première rangée seront prises pour la conversion.

> [!TIP]
> Vous pouvez utiliser une [instruction de laisser](letstatement.md) pour `toscalar()`la lisibilité de la requête lors de l’utilisation .

**Remarques**

`toscalar()`peut être calculé un nombre constant de fois pendant l’exécution de la requête.
En d’autres termes, `toscalar()` la fonction ne peut pas être appliquée au niveau de la rangée de (pour chaque ligne scénario).

**Exemples**

La requête suivante `Start` `End` évalue, `Step` et comme constantes scalaires `range` - et l’utiliser pour l’évaluation. 

```kusto
let Start = toscalar(print x=1);
let End = toscalar(range x from 1 to 9 step 1 | count);
let Step = toscalar(2);
range z from Start to End step Step | extend start=Start, end=End, step=Step
```

|z|start|end|étape|
|---|---|---|---|
|1|1|9|2|
|3|1|9|2|
|5|1|9|2|
|7|1|9|2|
|9|1|9|2|