---
title: bag_merge ()-Azure Explorateur de données
description: Cet article décrit bag_merge () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 66a05cdc03b155b8fceace0af8d86807a10d0da4
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349361"
---
# <a name="bag_merge"></a>bag_merge()

Fusionne `dynamic` les conteneurs de propriétés dans un `dynamic` conteneur de propriétés avec toutes les propriétés fusionnées.

## <a name="syntax"></a>Syntaxe

`bag_merge(`*BAG1* `, ` *BAG2* `[` ,` *bag3*, ...])`

## <a name="arguments"></a>Arguments

* *BAG1... bagN*: propriété d’entrée `dynamic` -sacs. La fonction accepte entre 2 et 64 arguments.

## <a name="returns"></a>Retourne

Retourne un `dynamic` conteneur de propriétés. Résultats de la fusion de tous les objets de jeu de propriétés d’entrée. Si une clé apparaît dans plusieurs objets d’entrée, une valeur arbitraire (parmi les valeurs possibles pour cette clé) est choisie.

## <a name="example"></a>Exemple

Expression :

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result = bag_merge(
   dynamic({'A1':12, 'B1':2, 'C1':3}),
   dynamic({'A2':81, 'B2':82, 'A1':1}))
```

|result|
|---|
|{<br>  « A1 » : 12,<br>  « B1 » : 2,<br>  « C1 » : 3,<br>  « A2 » : 81,<br>  « B2 » : 82<br>}|
