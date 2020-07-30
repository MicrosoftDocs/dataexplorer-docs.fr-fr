---
title: toscalar ()-Azure Explorateur de données
description: Cet article décrit toscalar () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 649d09fcf6d228714fdf20b40c81b2a2552374e6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87340256"
---
# <a name="toscalar"></a>toscalar()

Retourne une valeur de constante scalaire de l’expression évaluée. 

Cette fonction est utile pour les requêtes qui requièrent des calculs intermédiaires. Par exemple, calculez le nombre total d’événements, puis utilisez le résultat pour filtrer les groupes qui dépassent un certain pourcentage de tous les événements.

## <a name="syntax"></a>Syntaxe

`toscalar(`*Formule*`)`

## <a name="arguments"></a>Arguments

* *Expression*: expression qui sera évaluée pour la conversion scalaire.

## <a name="returns"></a>Retourne

Valeur de constante scalaire de l’expression évaluée.
Si le résultat est un tableau, la première colonne et la première ligne sont prises pour la conversion.

> [!TIP]
> Vous pouvez utiliser une [instruction Let](letstatement.md) pour améliorer la lisibilité de la requête lors de l’utilisation de `toscalar()` .

**Remarques**

`toscalar()`peut être calculé un nombre constant de fois pendant l’exécution de la requête.
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
