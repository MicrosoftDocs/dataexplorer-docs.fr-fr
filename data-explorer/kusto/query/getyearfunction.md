---
title: GetYear ()-Azure Explorateur de données | Microsoft Docs
description: Cet article décrit GetYear () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: a820d05b453be7cc991f8068644d33ff990115fc
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92251148"
---
# <a name="getyear"></a>getyear()

Retourne la partie année de l' `datetime` argument.

## <a name="example"></a>Exemple

```kusto
T
| extend year = getyear(datetime(2015-10-12))
// year == 2015
```