---
title: opérateur distinct de l’échantillon - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit l’opérateur d’échantillons distincts dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b6d6c77aef3a7e2c6d99af792062d9f1a6215f51
ms.sourcegitcommit: 436cd515ea0d83d46e3ac6328670ee78b64ccb05
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81663647"
---
# <a name="sample-distinct-operator"></a>opérateur sample-distinct

Renvoie une seule colonne qui contient le nombre spécifié de valeurs distinctes de la colonne demandée. 

la saveur par défaut (et actuellement seulement) de l’opérateur tente de retourner une réponse aussi rapidement que possible (plutôt que d’essayer de faire un échantillon équitable)

```kusto
T | sample-distinct 5 of DeviceId
```

**Syntaxe**

*T* `| sample-distinct` *NumberOfValues* `of` *ColumnName*

**Arguments**
* *NumberOfValues*: Le nombre de valeurs distinctes de *T* à revenir. Vous pouvez spécifier n’importe quelle expression numérique.

**Conseils**

 Peut être pratique pour échantillonner une population en `sample-distinct` mettant `in` dans une déclaration de laisser et plus tard filtrer à l’aide de l’opérateur (voir l’exemple) 

 Si vous voulez les valeurs supérieures plutôt qu’un simple échantillon, vous pouvez utiliser l’opérateur [top-hitters](tophittersoperator.md) 

 si vous souhaitez échantillonner des lignes de données (plutôt que des valeurs d’une colonne spécifique), consultez [l’opérateur de l’échantillon](sampleoperator.md)

**Exemples**  

Obtenez 10 valeurs distinctes d’une population

```kusto
StormEvents | sample-distinct 10 of EpisodeId

```

Échantillonnez une population et faites davantage de calculs en sachant que la synthèse ne dépassera pas les limites de requête. 

```kusto
let sampleEpisodes = StormEvents | sample-distinct 10 of EpisodeId;
StormEvents 
| where EpisodeId in (sampleEpisodes) 
| summarize totalInjuries=sum(InjuriesDirect) by EpisodeId
```