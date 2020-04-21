---
title: Débordements - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit Débordements dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 22db905788e1313cad2eb229239a390c28bcd5c2
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81523152"
---
# <a name="overflows"></a>Dépassement de capacité

Un débordement se produit lorsque le résultat d’un calcul est trop grand pour le type de destination.
Ce phénomène conduit généralement à un [échec partiel de requête.](partialqueryfailures.md)

Par exemple, la requête suivante entraînera un débordement :

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

La mise en `percentilesw()` œuvre `Weight` de Kusto accumule l’expression de valeurs « assez proches ».
Dans ce cas, l’accumulation déclenche un débordement car il ne rentre pas dans un intégrant signé 64 bits.

Habituellement, cependant, les débordements sont le résultat d’un "bug" dans la requête, puisque Kusto utilise 64 types de bits pour les calculs arithmétiques.
La meilleure ligne de conduite dans de tels cas est d’identifier à partir du message d’erreur qui fonction ou agrégation déclenché un débordement et de s’assurer que ses arguments d’entrée s’évaluent aux valeurs qui ont du sens.
