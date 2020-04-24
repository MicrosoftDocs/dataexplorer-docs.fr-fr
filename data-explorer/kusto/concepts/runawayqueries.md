---
title: Questions d’emballement - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit les requêtes Runaway dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 076afea64cf3c4405f37e9e4014584b90d58ca07
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81522982"
---
# <a name="runaway-queries"></a>Perte de contrôle des requêtes

Une *requête en fuite* est une sorte d’échec partiel de [requête](partialqueryfailures.md) qui se produit quand une limite interne de [requête](querylimits.md) a été dépassée pendant l’exécution de requête.

Par exemple, l’erreur suivante peut être signalée :`HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

Il existe plusieurs pistes d’action possibles lorsque cela se produit :
* Modifier la requête pour consommer moins de ressources. Par exemple, si l’erreur indique que l’ensemble de résultats de requête est trop grand, vous pouvez essayer de limiter le nombre d’enregistrements retournés par la requête (en utilisant [l’opérateur de prise](../query/takeoperator.md) ou en ajoutant des clauses supplémentaires), ou réduire le nombre de [colonnes](../query/whereoperator.md)retournées par la requête (en utilisant l’opérateur du [projet](../query/projectoperator.md) ou l’opérateur [de projet),](../query/projectawayoperator.md)ou utiliser l’opérateur de [résumé](../query/summarizeoperator.md) pour obtenir des données agrégées, etc.
* Augmenter temporairement la limite de requête pertinente pour cette requête (voir **La mémoire maximale par résultat a défini l’itérateur** sous les [limites de requête).](querylimits.md)  
  Notez que cela n’est pas recommandé en général, car les limites existent pour protéger le cluster spécifiquement pour s’assurer qu’une seule requête ne perturbe pas les requêtes simultanées en cours d’exécution sur le cluster.
  
  
  