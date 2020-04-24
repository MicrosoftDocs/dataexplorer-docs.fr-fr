---
title: dayofmonth() - Azure Data Explorer (fr) Microsoft Docs
description: Cet article décrit dayofmonth() dans Azure Data Explorer.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 791d85d4a8f89487e65ef68ecc605f907e63e1ea
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81516335"
---
# <a name="dayofmonth"></a>dayofmonth()

Retourne le numéro d’integer représentant le numéro de jour du mois donné

```kusto
dayofmonth(datetime(2015-12-14)) == 14
```

**Syntaxe**

`dayofmonth(`*a_date*`)`

**Arguments**

* `a_date` : une `datetime`.

**Retourne**

`day number`du mois donné.