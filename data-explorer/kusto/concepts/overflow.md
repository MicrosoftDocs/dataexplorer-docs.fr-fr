---
title: Débordements-Azure Explorateur de données
description: Cet article décrit les dépassements de capacité dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 063165c91319ef5f4183a8ce8f83364fd8188072
ms.sourcegitcommit: f7101c6b41ec250d05f4cb6092e2939958b37b40
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/03/2020
ms.locfileid: "84328957"
---
# <a name="overflows"></a>Dépassement de capacité

Un dépassement de capacité se produit lorsque le résultat d’un calcul est trop grand pour le type de destination.
Le dépassement de capacité provoque généralement un [échec de requête partielle](partialqueryfailures.md).

Par exemple, la requête suivante entraînera un dépassement de capacité.

```kusto
let Weight = 92233720368547758;
range x from 1 to 3 step 1
| summarize percentilesw(x, Weight * 100, 50)
```

`percentilesw()`L’implémentation de Kusto accumule l' `Weight` expression pour les valeurs « suffisamment proches ».
Dans ce cas, l’accumulation déclenche un dépassement de capacité, car il ne s’ajuste pas à un entier 64 bits signé.

En règle générale, les débordements sont le résultat d’un « bogue » dans la requête, car Kusto utilise des types 64 bits pour les calculs arithmétiques.
La meilleure solution consiste à examiner le message d’erreur et à identifier la fonction ou l’agrégation qui a déclenché le dépassement de capacité. Assurez-vous que les arguments d’entrée correspondent aux valeurs qui sont logiques.
