---
title: GetMonth ()-Azure Explorateur de données
description: Cet article décrit GetMonth () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: 1b3f87c7be8f4b2e12fc0136c163c4df0766eed8
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252984"
---
# <a name="getmonth"></a>getmonth()

Obtient le numéro du mois (1 à 12) à partir d’une valeur datetime.

Autre alias : monthoyear ()

## <a name="example"></a>Exemple

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
