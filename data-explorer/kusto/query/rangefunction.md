---
title: Plage ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit la plage () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 2606746e89d645601fa53ed7f81d67ddae203c03
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87345910"
---
# <a name="range"></a>range()

Génère un tableau dynamique contenant une série de valeurs espacées de façon égale.

## <a name="syntax"></a>Syntaxe

`range(`*Démarrer* `,` *arrêter*[ `,` *étape*]`)` 

## <a name="arguments"></a>Arguments

* *Start*: valeur du premier élément dans le tableau résultant. 
* *Stop*: valeur du dernier élément dans le tableau résultant, ou valeur minimale supérieure au dernier élément du tableau résultant et dans un entier multiple de l' *étape* de *démarrage*.
* *Step*: différence entre deux éléments consécutifs du tableau. La valeur par défaut de l' *étape* est pour les valeurs `1` numériques et `1h` pour `timespan` ou`datetime`

## <a name="examples"></a>Exemples

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
