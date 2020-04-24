---
title: L’ensemble de résultats de requête a dépassé le ... limite - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’ensemble de résultats De Requête a dépassé l’interne ... limite dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: efb07e68922a88e2cf59ef1eefeabd3af085aed4
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523016"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>L’ensemble de résultats de requête a dépassé le ... Limite

Un *ensemble de résultats de requête a dépassé l’interne ... limite* est une sorte [d’échec partiel](partialqueryfailures.md) de requête qui se produit lorsque le résultat de la requête a dépassé l’une des deux limites:
* Une limite au nombre`record count limit`d’enregistrements (, fixé par défaut à 500 000).
* Limite de la quantité totale`data size limit`de données (, fixée par défaut à 67 108 864 (64 Mo).) 

Il existe plusieurs pistes d’action possibles lorsque cela se produit :
* Modifier la requête pour consommer moins de ressources. Par exemple, vous pouvez essayer de limiter le nombre d’enregistrements retournés par la requête (à l’aide de [l’opérateur de prise](../query/takeoperator.md) ou en ajoutant des [clauses](../query/whereoperator.md)supplémentaires), ou réduire le nombre de colonnes retournées par la requête (en utilisant l’opérateur du [projet](../query/projectoperator.md) ou [l’opérateur de projet),](../query/projectawayoperator.md)ou utiliser l’opérateur [de résumé](../query/summarizeoperator.md) pour obtenir des données agrégées, etc.
* Augmenter temporairement la limite de requête pertinente pour cette requête (voir **la troncation des** résultats dans les [limites de la requête).](querylimits.md)  
  Notez que cela n’est pas recommandé en général, car les limites existent pour protéger le cluster spécifiquement pour s’assurer qu’une seule requête ne perturbe pas les requêtes simultanées en cours d’exécution sur le cluster.