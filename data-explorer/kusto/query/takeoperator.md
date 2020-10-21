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
ms.openlocfilehash: 5192bb2d752a5754ae36840611b9f7b0e84da256
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250727"
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

## <a name="does-kusto-support-paging-of-query-results"></a>Kusto prend-il en charge la pagination des résultats de la requête ?

Kusto ne fournit pas de mécanisme de pagination intégré.

Kusto est un service complexe qui optimise en permanence les données qu’il stocke pour fournir d’excellentes performances de requête sur les jeux de données volumineux. Tandis que la pagination est un mécanisme utile pour les clients sans état avec des ressources limitées, elle déplace la charge vers le service principal qui doit suivre les informations d’État du client. Par la suite, les performances et l’évolutivité du service principal sont fortement limitées.

Pour la prise en charge de la pagination, implémentez l’une des fonctionnalités suivantes :

* Exportation du résultat d’une requête vers un stockage externe et pagination des données générées.

* Écriture d’une application de niveau intermédiaire qui fournit une API de pagination avec état en mettant en cache les résultats d’une requête Kusto.

## <a name="see-also"></a>Voir aussi

* [opérateur sort](sortoperator.md)
* [opérateur top](topoperator.md)
* [Opérateur top-nested](topnestedoperator.md)