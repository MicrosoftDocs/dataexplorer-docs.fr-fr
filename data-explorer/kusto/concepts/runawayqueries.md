---
title: Requêtes d’échappement-Azure Explorateur de données
description: Cet article décrit les requêtes d’échappement dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4f2cf559863eecbd267855cd4a22f2dbe8673dc7
ms.sourcegitcommit: ee904f45e3eb3feab046263aa9956cb7780a056d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92356603"
---
# <a name="runaway-queries"></a>Perte de contrôle des requêtes

Une *requête d’échappement* est un type d' [échec de requête partielle](partialqueryfailures.md) qui se produit lorsque la limite d’une [requête](querylimits.md) interne a été dépassée pendant l’exécution de la requête. 

Par exemple, l’erreur suivante peut être signalée : `HashJoin operator has exceeded the memory budget during evaluation. Results may be incorrect or incomplete.`

Il existe plusieurs cours d’action possibles.
* Modifiez la requête pour consommer moins de ressources. Par exemple, si l’erreur indique que le jeu de résultats de la requête est trop grand, vous pouvez :
  * Limiter le nombre d’enregistrements retournés par la requête par
     * Utilisation de l' [opérateur Take](../query/takeoperator.md)
     * Ajout de [clauses WHERE](../query/whereoperator.md) supplémentaires
  * Réduire le nombre de colonnes retournées par la requête par
     * Utilisation de l' [opérateur Project](../query/projectoperator.md)
     * Utilisation de l' [opérateur Project-Away](../query/projectawayoperator.md)
     * Utilisation de l' [opérateur Project-Keep](../query/project-keep-operator.md)
  * Utilisez l' [opérateur de synthèse](../query/summarizeoperator.md) pour récupérer des données agrégées.
* Augmentez temporairement la limite de requête appropriée pour cette requête. Pour plus d’informations, consultez [limites de requête-limite de mémoire par itérateur](querylimits.md). Toutefois, cette méthode n’est pas recommandée. Les limites existent pour protéger le cluster et pour s’assurer qu’une seule requête n’interrompt pas les requêtes simultanées en cours d’exécution sur le cluster.
