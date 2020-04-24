---
title: matérialiser() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit matérialiser () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: dfaed8cf972a517c86717999b3e5423c0ddd1fb0
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512612"
---
# <a name="materialize"></a>materialize()

Permet de mettre en cache un résultat de sous-requête pendant le temps de l’exécution de requête d’une manière que d’autres sous-queries peuvent référencer le résultat partiel.

 
**Syntaxe**

`materialize(`*Expression*`)`

**Arguments**

* *expression*: Expression tabulaire à évaluer et mise en cache lors de l’exécution de la requête.

**Conseils**

* Utilisez materialize lorsque vous disposez de joint/union, où leurs opérandes incluent des sous-requêtes mutuelles ne pouvant être exécutées qu’une seule fois (voir les exemples ci-dessous).

* Utile également dans des scénarios lorsque nous avons besoin de branchements join/union.

* Materialize ne peut être utilisé que dans des instructions let en donnant un nom au résultat mis en cache.

* Matérialiser a une limite de taille de cache qui est **de 5 Go**. 
  Cette limite est par nœud de cluster et est mutuelle pour toutes les requêtes fonctionnant simultanément.
  Si une requête `materialize()` utilise et que le cache ne peut pas contenir de données supplémentaires, la requête avorte avec une erreur.

**Exemples**

En supposant que nous voulons générer un ensemble aléatoire de valeurs et nous sommes intéressés à trouver combien de valeurs distinctes nous avons, la somme de toutes ces valeurs et les 3 premières valeurs.

Cela peut être fait à l’aide [de lots](batches.md) et de matérialiser :

 ```kusto
let randomSet = materialize(range x from 1 to 30000000 step 1
| project value = rand(10000000));
randomSet
| summarize dcount(value);
randomSet
| top 3 by value;
randomSet
| summarize sum(value)

```