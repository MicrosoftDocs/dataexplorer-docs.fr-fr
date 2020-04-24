---
title: Batches - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Batches in Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: bd695a1e7ffd9980de2750d38ad9538eeb877538
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81518018"
---
# <a name="batches"></a>Lots

Une requête peut inclure plusieurs déclarations d’expression tabulaire, tant qu’elles`;`sont délimitées par un personnage semi-clon ( ) . La requête renvoie ensuite plusieurs résultats tabulaires, tels que produits par les énoncés d’expression tabulaires, et ordonnée conformément à l’ordre des déclarations contenues dans le texte de la requête.

Par exemple, la requête suivante produit deux résultats tabulaires. Les outils d’agent utilisateur peuvent ensuite afficher`Count of events in Florida` ces `Count of events in Guam`résultats avec le nom approprié associé à chacun (et, respectivement).

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Le lot est particulièrement utile pour les scénarios dans lesquels il existe un calcul courant qui est partagé par plusieurs sous-requêtes, comme pour les tableaux de bord. Si le calcul courant est complexe, il est recommandé de construire la requête de sorte qu’elle ne soit exécutée qu’une seule fois, en utilisant la [fonction matérialisée](./materializefunction.md):

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

Remarques :
* Préférez le [`materialize`](materializefunction.md) lotage et plus à l’aide de l’opérateur [de fourche](forkoperator.md).