---
title: Le jeu de résultats de la requête Kusto dépasse la limite interne-Azure Explorateur de données
description: Cet article décrit le jeu de résultats de la requête a dépassé la valeur interne... limite dans les Explorateur de données Azure.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 41a9851405ff85210bba7728a3737fc3b0a57f70
ms.sourcegitcommit: fd3bf300811243fc6ae47a309e24027d50f67d7e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83382384"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>Le jeu de résultats de la requête a dépassé la valeur interne... sépar

Un *jeu de résultats de requête a dépassé la valeur interne... Limit* est un type d' [échec de requête partielle](partialqueryfailures.md) qui se produit lorsque le résultat de la requête a dépassé l’une des deux limites suivantes :
* Limite du nombre d’enregistrements ( `record count limit` , définie par défaut sur 500 000).
* Limite de la quantité totale de données ( `data size limit` , définie par défaut sur 67 108 864 (64 Mo)). 

Lorsque cela se produit, plusieurs cours sont possibles :
* Modifiez la requête pour consommer moins de ressources. Par exemple, vous pouvez essayer de limiter le nombre d’enregistrements renvoyés par la requête (à l’aide de l' [opérateur Take](../query/takeoperator.md) ou en ajoutant des [clauses WHERE](../query/whereoperator.md)supplémentaires) ou de réduire le nombre de colonnes retournées par la requête (à l’aide de l’opérateur [Project](../query/projectoperator.md) ou de l' [opérateur Project-](../query/projectawayoperator.md)out) ou d’utiliser l' [opérateur de synthèse](../query/summarizeoperator.md) pour obtenir des données agrégées, etc.
* Augmentez temporairement la limite de requête appropriée pour cette requête (voir **troncation des résultats** sous [limites de requête](querylimits.md)).  
  Notez que cela n’est pas recommandé en général, car les limites existent pour protéger le cluster spécifiquement pour s’assurer qu’une seule requête n’interrompt pas les requêtes simultanées en cours d’exécution sur le cluster.