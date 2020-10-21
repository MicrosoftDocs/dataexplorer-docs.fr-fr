---
title: Lots-Azure Explorateur de données
description: Cet article décrit les lots dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: efd4b4c5387016bd66027bcf5e0722f8039f0837
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252627"
---
# <a name="batches"></a>Lots

Une requête peut inclure plusieurs instructions d’expression tabulaires, à condition qu’elles soient délimitées par un point-virgule ( `;` ). La requête retourne ensuite plusieurs résultats tabulaires. Les résultats sont générés par les instructions d’expression tabulaire et classés en fonction de l’ordre des instructions dans le texte de la requête.

Par exemple, la requête suivante génère deux résultats tabulaires. Les outils de l’agent utilisateur peuvent ensuite afficher ces résultats avec le nom approprié associé à chaque ( `Count of events in Florida` et `Count of events in Guam` , respectivement).

```kusto
StormEvents | where State == "FLORIDA" | count | as ['Count of events in Florida'];
StormEvents | where State == "GUAM" | count | as ['Count of events in Guam']
```

Le traitement par lots est utile dans les scénarios où un calcul commun est partagé par plusieurs sous-requêtes, par exemple pour les tableaux de bord. Si le calcul commun est complexe, utilisez la [fonction matérialiser ()](./materializefunction.md) et construisez la requête pour qu’elle ne soit exécutée qu’une seule fois :

```kusto
let m = materialize(StormEvents | summarize n=count() by State);
m | where n > 2000;
m | where n < 10
```

Remarques :
* Préférez le traitement par lot et l' [`materialize`](materializefunction.md) utilisation de l' [opérateur fourche](forkoperator.md).
