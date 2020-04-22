---
title: gamme () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit la gamme () dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 86558591e6312edd218230cda19a4afc17a13b27
ms.sourcegitcommit: e94be7045d71a0435b4171ca3a7c30455e6dfa57
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81744582"
---
# <a name="range"></a>range()

Génère un tableau dynamique contenant une série de valeurs à espace également.

**Syntaxe**

`range(`*arrêt de départ* `,` *stop*[`,` *étape*]`)` 

**Arguments**

* *début*: La valeur du premier élément dans le tableau résultant. 
* *arrêt*: La valeur du dernier élément dans le tableau résultant, ou la moindre valeur qui est supérieure au dernier élément dans le tableau résultant et dans un multiple *d’étape* integer dès le *début.*
* *étape*: La différence entre deux éléments consécutifs du tableau. La valeur par `1` défaut pour `1h` `timespan` *l’étape* est pour le numérique et pour ou pour`datetime`

**Exemples**

L’exemple suivant retourne `[1, 4, 7]`:

```kusto
T | extend r = range(1, 8, 3)
```

L’exemple suivant retourne un tableau contenant tous les jours de l’année 2015 :

```kusto
T | extend r = range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

L’exemple suivant retourne `[1,2,3]`:

```kusto
range(1, 3)
```

L’exemple suivant retourne `["01:00:00","02:00:00","03:00:00","04:00:00","05:00:00"]`:

```kusto
range(1h, 5h)
```
