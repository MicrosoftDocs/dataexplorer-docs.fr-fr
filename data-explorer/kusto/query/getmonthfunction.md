---
title: GetMonth ()-Azure Explorateur de données
description: Cet article décrit GetMonth () dans Azure Explorateur de données.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/22/2020
ms.openlocfilehash: e04abb6eed95e5129d6878486e3781173cbf0557
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83226786"
---
# <a name="getmonth"></a>getmonth()

Obtient le numéro du mois (1 à 12) à partir d’une valeur datetime.

Autre alias : monthoyear ()

**Exemple**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
print month = getmonth(datetime(2015-10-12))
```
