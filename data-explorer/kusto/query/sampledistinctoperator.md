---
title: Sample-distinct, opérateur-Azure Explorateur de données
description: Cet article décrit l’exemple d’opérateur distinct dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cfd8385a5dc8f959e1fe195bfe333a6868f55cb4
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242736"
---
# <a name="sample-distinct-operator"></a>opérateur sample-distinct

Renvoie une seule colonne qui contient le nombre spécifié de valeurs distinctes de la colonne demandée. 

la version par défaut (et actuellement uniquement) de l’opérateur tente de retourner une réponse aussi rapidement que possible (au lieu d’essayer de créer un échantillon équitable)

```kusto
T | sample-distinct 5 of DeviceId
```

## <a name="syntax"></a>Syntaxe

*T* `| sample-distinct` *NumberOfValues* `of` *ColumnName*

## <a name="arguments"></a>Arguments
* *NumberOfValues*: nombre de valeurs distinctes de *T* à retourner. Vous pouvez spécifier n’importe quelle expression numérique.

**Conseils**

 Peut être pratique pour échantillonner une population en plaçant `sample-distinct` une instruction Let et un filtre ultérieur à l’aide de l' `in` opérateur (Voir l’exemple) 

 Si vous souhaitez obtenir les valeurs les plus importantes plutôt qu’un simple exemple, vous pouvez utiliser l’opérateur [Top-Hitters](tophittersoperator.md) 

 Si vous souhaitez échantillonner des lignes de données (plutôt que des valeurs d’une colonne spécifique), reportez-vous à l' [exemple d’opérateur](sampleoperator.md)

## <a name="examples"></a>Exemples  

Obtenir 10 valeurs distinctes d’une population

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

Échantillonnez une population et faites davantage de calculs en sachant que la synthèse ne dépassera pas les limites de requête. 

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```
