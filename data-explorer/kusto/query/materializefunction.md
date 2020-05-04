---
title: matérialiser ()-Azure Explorateur de données
description: Cet article décrit matérial () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/21/2019
ms.openlocfilehash: e721e5809d3b0445fecc0609668332b66ef39db8
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737332"
---
# <a name="materialize"></a>materialize()

Permet de mettre en cache le résultat d’une sous-requête pendant l’exécution de la requête d’une manière que d’autres sous-requêtes peuvent référencer le résultat partiel.

 
**Syntaxe**

`materialize(`*expression*`)`

**Arguments**

* *expression*: expression tabulaire à évaluer et à mettre en cache lors de l’exécution de la requête.

**Conseils**

* Utilisez matérialisation avec JOIN ou Union lorsque leurs opérandes ont des sous-requêtes réciproques qui peuvent être exécutées une seule fois. Consultez les exemples ci-dessous.

* Utile également dans des scénarios lorsque nous avons besoin de branchements join/union.

* Matérialiser ne peut être utilisé que dans les instructions Let si vous attribuez un nom au résultat mis en cache.


* La matérialisation a une limite de taille de cache de **5 Go**. 
  Cette limite est définie par nœud de cluster et est mutuelle pour toutes les requêtes qui s’exécutent simultanément.
  Si une requête utilise `materialize()` et que le cache ne peut pas contenir plus de données, la requête s’interrompt avec une erreur.

**Exemples**

Nous souhaitons générer un ensemble aléatoire de valeurs et souhaitez en savoir plus : 
 * nombre de valeurs distinctes que nous avons 
 * somme de toutes ces valeurs 
 * les trois premières valeurs

Cette opération peut être effectuée à l’aide de [traitements](batches.md) et matérialiser :

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