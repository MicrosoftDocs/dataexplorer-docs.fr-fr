---
title: heure d’aujourd’hui () - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit hourofday() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 08fdb45af5eec7f71d491725ea58a7d72c06371b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514074"
---
# <a name="hourofday"></a>hourofday()

Retourne le numéro d’intégrant représentant le numéro d’heure de la date donnée

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

**Syntaxe**

`hourofday(`*a_date*`)`

**Arguments**

* `a_date` : une `datetime`.

**Retourne**

`hour number`du jour (0-23).