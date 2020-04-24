---
title: make_timespan() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit make_timespan() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 31301f684ea700cf89e9234c4c43adab068319b7
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81512765"
---
# <a name="make_timespan"></a>make_timespan()

Crée une valeur scalaire de [timespan](./scalar-data-types/timespan.md) à partir de la période spécifiée.

```kusto
make_timespan(1,12,30,55.123) == time(1.12:30:55.123)
```

**Syntaxe**

`make_timespan(`*heure*,*minute*`)`

`make_timespan(`*heure*,*minute*,*deuxième*`)`

`make_timespan(`*jour*,*heure*,*minute*,*deuxième*`)`

**Arguments**

* *jour*: jour (valeur positive de l’intégrant)
* *heure*: heure (valeur integer, de 0 à 23)
* *minute*: minute (valeur integer, de 0 à 59)
* *deuxième*: deuxième (valeur réelle, de 0 à 59.99999999)

**Retourne**

Si la création est réussie, le résultat sera une valeur [de temps,](./scalar-data-types/timespan.md) sinon, le résultat sera nul.
 
**Exemple**

```kusto
print ['timespan'] = make_timespan(1,12,30,55.123)

```

|intervalle de temps|
|---|
|1.12:30:55.1230000|


