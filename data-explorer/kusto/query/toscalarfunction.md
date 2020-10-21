---
title: toscalar ()-Azure Explorateur de données
description: Cet article décrit toscalar () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 1210a820b4b3c8790d218ba53992da0255028de2
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252015"
---
# <a name="toscalar"></a>toscalar()

Retourne une valeur de constante scalaire de l’expression évaluée. 

Cette fonction est utile pour les requêtes qui requièrent des calculs intermédiaires. Par exemple, calculez le nombre total d’événements, puis utilisez le résultat pour filtrer les groupes qui dépassent un certain pourcentage de tous les événements.

## <a name="syntax"></a>Syntaxe

`toscalar(`*Formule*`)`

## <a name="arguments"></a>Arguments

* *Expression*: expression qui sera évaluée pour la conversion scalaire.

## <a name="returns"></a>Retours

Valeur de constante scalaire de l’expression évaluée.
Si le résultat est un tableau, la première colonne et la première ligne sont prises pour la conversion.

> [!TIP]
> Vous pouvez utiliser une [instruction Let](letstatement.md) pour améliorer la lisibilité de la requête lors de l’utilisation de `toscalar()` .

**Notes**

`toscalar()` peut être calculé un nombre constant de fois pendant l’exécution de la requête.
La `toscalar()` fonction ne peut pas être appliquée au niveau de la ligne (scénario pour chaque ligne).

## <a name="examples"></a>Exemples

Évaluez `Start` `End` `Step` les constantes, et en tant que constantes scalaires, et utilisez le résultat pour l' `range` évaluation.

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
