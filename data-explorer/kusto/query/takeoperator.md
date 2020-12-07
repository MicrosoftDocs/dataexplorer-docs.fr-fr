---
title: Take, opérateur-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit l’opérateur Take dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 569554379ffe672ed75fd15da127f03fea35f6d1
ms.sourcegitcommit: 2804e3fe40f6cf8e65811b00b7eb6a4f59c88a99
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748380"
---
# <a name="take-operator"></a>opérateur take

Retourne jusqu’au nombre de lignes spécifié.

```kusto
T | take 5
```

Il n’existe aucune garantie quant aux enregistrements qui sont retournés, à moins que les données sources soient triées.

> [!NOTE]
> `take` est un moyen simple, rapide et efficace d’afficher un petit échantillon d’enregistrements lorsque vous parcourez les données de manière interactive, mais sachez qu’elles ne garantissent pas la cohérence de leurs résultats lors de l’exécution à plusieurs moments, même si le jeu de données n’a pas changé.
> Même si le nombre de lignes retournées par la requête n’est pas explicitement limité par la requête (aucun `take` opérateur n’est utilisé), Kusto limite ce nombre par défaut. Pour plus d’informations, consultez [limites de requête Kusto](../concepts/querylimits.md).

## <a name="syntax"></a>Syntaxe

`take`*NumberOfRows* 
 NumberOfRows `limit` *NumberOfRows*

( `take` et `limit` sont des synonymes).

## <a name="paging-of-query-results"></a>Pagination des résultats de la requête

Les méthodes d’implémentation de la pagination sont les suivantes :

* Exportez le résultat d’une requête dans un stockage externe et en Paginant les données générées.
* Écrivez une application de niveau intermédiaire qui fournit une API de pagination avec état en mettant en cache les résultats d’une requête Kusto.
* Utilisez la pagination dans les résultats de la [requête stockée](../management/stored-query-results.md#pagination) .


## <a name="see-also"></a>Voir aussi

* [opérateur sort](sortoperator.md)
* [opérateur top](topoperator.md)
* [Opérateur top-nested](topnestedoperator.md)
