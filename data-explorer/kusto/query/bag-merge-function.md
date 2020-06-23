---
title: bag_merge ()-Azure Explorateur de données
description: Cet article décrit bag_merge () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspod
ms.reviewer: elgevork
ms.service: data-explorer
ms.topic: reference
ms.date: 06/18/2020
ms.openlocfilehash: 0a23f6ece8be3ba451c1f61a90eb65452b68f9ce
ms.sourcegitcommit: e87b6cb2075d36dbb445b16c5b83eff7eaf3cdfa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2020
ms.locfileid: "85265055"
---
# <a name="bag_merge"></a>bag_merge()

Fusionne `dynamic` les conteneurs de propriétés dans un `dynamic` conteneur de propriétés avec toutes les propriétés fusionnées.

**Syntaxe**

`bag_merge(`*BAG1* `, ` *BAG2* `[` ,` *bag3*, ...])`

**Arguments**

* *BAG1... bagN*: propriété d’entrée `dynamic` -sacs. La fonction accepte entre 2 et 64 arguments.

**Retourne**

Retourne un `dynamic` conteneur de propriétés. Résultats de la fusion de tous les objets de jeu de propriétés d’entrée. Si une clé apparaît dans plusieurs objets d’entrée, une valeur arbitraire (parmi les valeurs possibles pour cette clé) est choisie.

**Exemple**

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
