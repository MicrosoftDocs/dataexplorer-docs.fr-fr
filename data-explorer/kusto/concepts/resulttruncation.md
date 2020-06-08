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
ms.openlocfilehash: 9706cce08a99989441aa657c5d85465294be837e
ms.sourcegitcommit: 188f89553b9d0230a8e7152fa1fce56c09ebb6d6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2020
ms.locfileid: "84512416"
---
# <a name="query-result-set-has-exceeded-the-internal--limit"></a>Le jeu de résultats de la requête a dépassé la valeur interne... sépar

Un *jeu de résultats de requête a dépassé la valeur interne... Limit* est un type d' [échec de requête partielle](partialqueryfailures.md) qui se produit lorsque le résultat de la requête a dépassé l’une des deux limites suivantes :
* Limite du nombre d’enregistrements ( `record count limit` , définie par défaut sur 500 000)
* Limite de la quantité totale de données ( `data size limit` , définie par défaut sur 67 108 864 (64 Mo))

Il existe plusieurs cours d’action possibles :

* Modifiez la requête pour consommer moins de ressources. 
  Par exemple, vous pouvez :
  * Limiter le nombre d’enregistrements retournés par la requête à l’aide de l' [opérateur Take](../query/takeoperator.md) ou en ajoutant des [clauses WHERE](../query/whereoperator.md) supplémentaires
  * Essayez de réduire le nombre de colonnes retournées par la requête. Utiliser l' [opérateur Project](../query/projectoperator.md)ou l' [opérateur Project-Away](../query/projectawayoperator.md))
  * Utiliser l' [opérateur de synthèse](../query/summarizeoperator.md) pour récupérer des données agrégées
* Augmentez temporairement la limite de requête appropriée pour cette requête. Pour plus d’informations, consultez **troncation des résultats** sous [limites de requête](querylimits.md)).

 > [!NOTE] 
 > Nous vous déconseillons d’augmenter la limite des requêtes, car les limites existent pour protéger le cluster. Les limites permettent de s’assurer qu’une seule requête n’interrompt pas les requêtes simultanées en cours d’exécution sur le cluster.
  
